<properties 
    pageTitle="電腦學習建議 API 文件 |Microsoft Azure" 
    description="Azure 電腦學習建議 API 文件，在 Microsoft Azure Marketplace 提供的建議引擎。" 
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
    ms.author="LuisCa"/>

#<a name="azure-machine-learning-recommendations-api-documentation"></a>Azure 電腦學習建議 API 文件

>[AZURE.NOTE]您應該開始使用，而非此版本的建議 API 認知服務。 建議認知服務會取代這項服務，並會那里開發所有的新功能。 有新的功能，例如批次處理支援、 獲得更佳的 API 檔案總管，取得更簡潔的 API 表面、 更加一致註冊/計費體驗等。
> 深入瞭解[移轉至新的認知服務](http://aka.ms/recomigrate)

本文描述 Microsoft Azure 電腦學習建議 Api。


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1.一般的概觀
這份文件是 API 參照。 您應該開頭 「 Azure 電腦學習建議-快速入門 」 的文件。

Azure 電腦學習建議 API 可分為以下邏輯群組︰

- <ins>限制</ins>-建議 API 限制。
- <ins>一般資訊</ins>-驗證，服務 URI 及版本設定的詳細資訊。
- <ins>模型基本</ins>-Api，可讓您執行基本作業模型 （例如建立、 更新及刪除模型）。
- <ins>進階的模型</ins>-Api，可讓您取得獲得資料深入見解進階的模型。
- <ins>模型商務規則</ins>-Api，讓您管理商務規則的模型建議結果。
- <ins>目錄</ins>-Api，可讓您執行基本作業模型目錄。 目錄中包含中繼資料的詳細資訊的使用情況資料的項目。
- <ins>功能</ins>-Api，讓，取得深入見解項目上，將類別目錄，以及如何使用此資訊來建立更好的建議。
- <ins>使用資料</ins>-Api，可讓您執行的模型使用情況資料的基本作業。 基本表單中的使用情況資料組成的一組與 #60; 使用者識別碼與 #62; 包含資料列，& #60; itemId & #62;。
- <ins>建立</ins>-Api 的觸發程序模型建立可讓您執行此建立相關的基本作業。 您有寶貴的使用情況資料後，您可以觸發模型建立。
- <ins>建議</ins>的 Api，可讓您使用建議，一旦建立模型的結尾。
- <ins>使用者資料</ins>-Api，讓您電腦上使用者使用情況資料的資訊。
- <ins>通知</ins>-Api，可讓您會收到通知，在您的 API 作業相關的問題。 （例如，您要報告透過資料擷取與大部分的事件處理失敗的狀況。 錯誤通知將會引發。）

##<a name="2-limitations"></a>2.限制

- 每個訂閱的模型數目上限為 10。
- 每個模型建置數目上限為 20。
- 目錄可以保留的項目數目上限為 100000。
- 使用點會保留的數目上限為 ~ x 5000000。 從最舊會被刪除如果要上傳新檔案，或報告。
- 可傳送文章 （例如匯入目錄資料，匯入使用情況資料） 中的資料大小上限為 200 MB。
- 取得建議時可以要求的項目數目上限為 150。

##<a name="3-apis---general-information"></a>3.Api-一般資訊

###<a name="31-authentication"></a>3.1。 驗證
請遵循驗證相關的 Microsoft Azure Marketplace 指導方針。 服務商場支援 [基本] 或 [OAuth 驗證方法。

###<a name="32-service-uri"></a>3.2 捨位。 服務 URI
服務根 Azure 電腦學習建議 Api URI 是[以下。](https://api.datamarket.azure.com/amla/recommendations/v3/)

完整的服務 URI 表示使用 OData 規格的項目。  

###<a name="33-api-version"></a>3.3。 API 版本
每個 API 呼叫必須，結束時，呼叫應該會設定為 1.0 的 apiVersion 查詢參數。

###<a name="34-ids-are-case-sensitive"></a>3.4。 識別碼會區分大小寫
識別碼，所傳回的任何的 Api，會區分大小寫，而且會因此時做為 API 的後續通話中的參數傳遞。 例如，模型識別碼和目錄識別碼會區分大小寫。

##<a name="4-recommendations-quality-and-cold-items"></a>4.建議品質和低溫的項目

###<a name="41-recommendation-quality"></a>4.1。 建議品質

建立建議模型通常是足以讓系統提供建議。 不過，建議品質有所不同處理使用狀況] 和 [類別目錄的範圍。 例如，如果您有很多低溫項目 （不重要的使用方式的項目），系統會有困難提供建議的項目，或與建議使用這類項目。 若要解決低溫的項目問題，系統會允許中繼資料的項目，以增強建議使用。 此中繼資料被指功能。 一般功能可在活頁簿的作者或影片的動作項目。 透過在表單中的索引鍵/值字串目錄提供功能。 目錄檔案的完整的格式，請參閱[匯入目錄] 區段中](#81-import-catalog-data)。 

###<a name="42-rank-build"></a>4.2。 Rank 建立

功能可以增強建議模型，但若要執行此作業需要使用的有意義的功能。 針對此目的推出新組建-rank 的建立。 此建立會評等功能的用途。 有意義的功能是等級 2 及設定與功能。
了解哪些功能有意義，觸發建議組建使用有意義的功能清單 （或子清單）。 就可以使用這些功能來加強暖色調陰影的項目和低溫的項目。 若要使用的暖色調陰影的項目，`UseFeatureInModel`應設定建立參數。 若要使用功能低溫的項目，`AllowColdItemPlacement`應該啟用建立參數。
注意︰ 並非可以啟用`AllowColdItemPlacement`沒有啟用`UseFeatureInModel`。

###<a name="43-recommendation-reasoning"></a>4.3。 建議的原因

建議的原因是另一個部分功能的使用方式。 Azure 電腦學習建議引擎確實想使用功能 （又名提供建議說明 弄），而產生建議消費者從建議的項目中的多個信賴度。
若要啟用原因，`AllowFeatureCorrelation`和`ReasoningFeatureList`參數應該之前要求建議建立的設定。


##<a name="5-model-basic"></a>5。 基本模型

###<a name="51-create-model"></a>5.1。 建立模型
建立 「 建立模型 」 要求。

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

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Create A New Model</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-05T06:35:21Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">CreateANewModelEntity2</title>
    <updated>2014-10-05T06:35:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/CreateModel?modelName='MyFirstModel'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###<a name="52-get-model"></a>5.2。 取得模型
建立 「 取得模型 」 要求。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/GetModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   識別碼  |   唯一識別碼 （區分大小寫） 模型 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

下列項目] 下，您可以找到模型資料︰

- `feed/entry/content/properties/Id`模型的唯一識別碼。
- `feed/entry/content/properties/Name`模型名稱。
- `feed/entry/content/properties/Date`模型建立日期。
- `feed/entry/content/properties/Status`-模型狀態。 下列其中一項︰
    - 建立-模型會建立並不包含目錄及使用方式。
    - ReadyForBuild-模型建立，並且包含目錄及使用方式。
- `feed/entry/content/properties/HasActiveBuild`-表示已成功建立模型。
- `feed/entry/content/properties/BuildId`模型使用中建立的識別碼。
- `feed/entry/content/properties/Mpr`-模型平均數的百分位數排名 (MPR-如需詳細資訊，請參閱 ModelInsight)。
- `feed/entry/content/properties/UserName`模型內部的使用者名稱。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Get A List Of All Models</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T14:35:51Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
        <d:Name m:type="Edm.String">vah-11111</d:Name>
        <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
        <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
        <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
        <d:BuildId m:type="Edm.String">-1</d:BuildId>
        <d:Mpr m:type="Edm.String">0</d:Mpr>
        <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
        <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
        <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
        <d:Description m:type="Edm.String">short description</d:Description>
        <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
      </m:properties>
    </content>
      </entry>
    </feed>

###<a name="53-get-all-models"></a>5.3。 取得所有模型
擷取目前使用者的所有模型。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetAllModels?apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/GetAllModels?apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

- `feed/entry/content/properties/Id`模型的唯一識別碼。
- `feed/entry/content/properties/Name`模型名稱。
- `feed/entry/content/properties/Date`模型建立日期。
- `feed/entry/content/properties/Status`-模型狀態。 下列其中一項︰
  - 建立-模型會建立並不包含目錄及使用方式。
  - ReadyForBuild-模型建立，並且包含目錄及使用方式。
- `feed/entry/content/properties/HasActiveBuild`-表示已成功建立模型。
- `feed/entry/content/properties/BuildId`模型使用中建立的識別碼。
- `feed/entry/content/properties/Mpr`-模型 MPR （如需詳細資訊，請參閱 ModelInsight）。
- `feed/entry/content/properties/UserName`模型內部的使用者名稱。
- `feed/entry/content/properties/UsageFileNames`-的模型使用檔案，並以逗號分隔的清單。
- `feed/entry/content/properties/CatalogId`模型目錄識別碼。
- `feed/entry/content/properties/Description`-模型的描述。
- `feed/entry/content/properties/CatalogFileName`模型目錄檔案名稱。

OData XML


    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get A List Of All Models</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-28T14:35:51Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfAllModelsEntity</title>
    <updated>2014-10-28T14:35:51Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetAllModels?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">68b232f2-1037-45f7-8f49-af822695ee63</d:Id>
                    <d:Name m:type="Edm.String">vah-11111</d:Name>
                    <d:Date m:type="Edm.String">10/28/2014 2:16:07 PM</d:Date>
                    <d:Status m:type="Edm.String">ReadyForBuild</d:Status>
                    <d:HasActiveBuild m:type="Edm.String">false</d:HasActiveBuild>
                    <d:BuildId m:type="Edm.String">-1</d:BuildId>
                    <d:Mpr m:type="Edm.String">0</d:Mpr>
                    <d:UsageFileNames m:type="Edm.String">ImplicitMatrix10_Guid_small.txt, ImplicitMatrix11_Guid_small.txt</d:UsageFileNames>
                    <d:CatalogId m:type="Edm.String">626babdb-cab6-43a6-82ef-4fb86345700c</d:CatalogId>
                    <d:UserName m:type="Edm.String">89dc4722-03ba-4f90-8821-b1db388408b5@dm.com</d:UserName>
                    <d:Description m:type="Edm.String">short description</d:Description>
                    <d:CatalogFileName m:type="Edm.String">catalog10_small.txt</d:CatalogFileName>
                </m:properties>
            </content>
        </entry>
    </feed>

###<a name="54-update-model"></a>5.4。 更新模型

您可以更新模型描述或作用中建立識別碼。<br>
<ins>作用中建立 ID</ins> -每一個建立的每個模型已建立的識別碼。 作用中建立 ID 是模型的第一個的成功建立的每個新。 有作用中建立 ID，以及您執行動作後其他建置相同的模型，您必須明確地將其設為預設值建立識別碼如果您想要。 當您使用的建議，如果您沒有指定您想要使用的建置識別碼時，就會自動使用預設值。<br>
這個機制可讓您-生產-若要建立新的模型，以及您生產升級之前先測試有建議模型。


| HTTP 方法 | URI |
|:--------|:--------|
|保留     |`<rootURI>/UpdateModel?id=%27<modelId>%27&apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/UpdateModel?id=%279559872f-7a53-4076-a3c7-19d9385c1265%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   識別碼      | 唯一識別碼 （區分大小寫） 模型  |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | `<ModelUpdateParams xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">`<br>`<Description>New Description</Description>`<br>`<ActiveBuildId>-1</ActiveBuildId>`<br>` </ModelUpdateParams>`<br><br>請注意，是選擇性的描述與 ActiveBuildId 的 XML 標記。 如果您不想要設定描述或 ActiveBuildId，移除整個標記。|

**回應**︰

HTTP 狀態碼︰ 200

###<a name="55-delete-model"></a>5.5。 刪除模型
刪除現有的模型的識別碼。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteModel?id=%27<model_id>%27&apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/DeleteModel?id=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   識別碼  |   唯一識別碼 （區分大小寫） 模型 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
      <title type="text" />
      <subtitle type="text">Delete Model by Id</subtitle>
      <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'</id>
      <rights type="text" />
      <updated>2014-10-28T10:39:33Z</updated>
     <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'" />
      <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">DeleteModelByIdEntity</title>
    <updated>2014-10-28T10:39:33Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/DeleteModel?id='1cac7b76-def4-41f1-bc81-29b806adb1de'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:string m:type="Edm.String"></d:string>
      </m:properties>
    </content>
      </entry>
    </feed>

##<a name="6-model-advanced"></a>6。 進階的模型

###<a name="61-model-data-insight"></a>6.1。 模型資料充分
傳回此模型建立與使用情況資料的統計資料。

僅適用於建議建立。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetDataInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/GetDataInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

資料會傳回屬性的集合。

- `feed/entry/id/content/properties/key`-保留屬性名稱。
- `feed/entry/id/content/properties/value`-保留屬性值。

下表描述每個機碼表示的值。

|索引鍵|描述|
|:-----|:----|
| AvgItemLength | 每個項目的的不同使用者的平均數目。 |
| AvgUserLength | 不同的項目，每位使用者的平均數目。 |
| DensificationNumberOfItems | 無法和模型化的剪除項目之後的項目數目。 |
| DensificationNumberOfUsers | 使用點剪除使用者並無法和模型化的項目後的數字。 |
| DensificationNumberOfRecords | 使用點剪除使用者並無法和模型化的項目後的數字。 |
| MaxItemLength | 最常用項目的的不同使用者數目。 |
| MaxUserLength | 最大數目不同的項目，為使用者的詳細資訊。 |
| MinItemLength | 最大數目的項目的個別使用者的詳細資訊。 |
| MinUserLength | 最小數目不同的項目，為使用者的詳細資訊。 |
| RawNumberOfItems | 使用檔案中的項目數目。 |
| RawNumberOfUsers | 作任何修改之前的使用狀況點的數目。 |
| RawNumberOfRecords | 作任何修改之前的使用狀況點的數目。 |
| SamplingNumberOfItems | N/A |
| SamplingNumberOfRecords | N/A |
| SamplingNumberOfUsers | N/A |

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get data insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgItemLength</d:Key>
        <d:Value m:type="Edm.String">18.936</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">AvgUserLength</d:Key>
        <d:Value m:type="Edm.String">51.879</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfItems</d:Key>
        <d:Value m:type="Edm.String">2,000</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Key m:type="Edm.String">DensificationNumberOfRecords</d:Key>
        <d:Value m:type="Edm.String">37,872</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetDataInsightStatisticsEntity</title>
    <updated>2014-10-27T14:21:21Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
    <content type="application/xml">
        <m:properties>
            <d:Key m:type="Edm.String">DensificationNumberOfUsers</d:Key>
            <d:Value m:type="Edm.String">730</d:Value>
      </m:properties>
    </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxItemLength</d:Key>
                <d:Value m:type="Edm.String">4,704</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MaxUserLength</d:Key>
                <d:Value m:type="Edm.String">190</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinItemLength</d:Key>
                <d:Value m:type="Edm.String">8</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">MinUserLength</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">RawNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfItems</d:Key>
                <d:Value m:type="Edm.String">2,000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=13&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfRecords</d:Key>
                <d:Value m:type="Edm.String">72,022</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1</id>
        <title type="text">GetDataInsightStatisticsEntity</title>
        <updated>2014-10-27T14:21:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetDataInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=14&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">SampelingNumberOfUsers</d:Key>
                <d:Value m:type="Edm.String">9,044</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="62-model-insight"></a>6.2。 模型充分
傳回作用中建立的模型充分或 （如果有） 上特定的建立。

僅適用於建議建立。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |作用中建立識別碼︰<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetModelInsight?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>特定建立識別碼︰<br>`<rootURI>/GetModelInsight?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   buildId |   選用-識別建置成功的數字。 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

資料會傳回屬性的集合。

- `feed/entry/id/content/properties/key`
- `feed/entry/id/content/properties/value`


下表描述每個機碼表示的值。

| 索引鍵 | 描述 |
|:---- |:----|
| CatalogCoverage | 目錄] 的部分可以與使用模式和模型化。 其餘的項目會需要的內容為基礎的功能。 |
| Mpr | 這表示模型的百分位數排名。 Lower 較佳。 |
| NumberOfDimensions | 使用矩陣 factorization 演算法的維度數目。 |


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get model insight statistics</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">CatalogCoverage</d:Key>
                <d:Value m:type="Edm.String">1.000</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">Mpr</d:Key>
                <d:Value m:type="Edm.String">0.367</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetModelInsightStatisticsEntity</title>
        <updated>2014-10-27T14:27:11Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelInsight?modelId='6254b40d-0514-49cb-a298-b81256d2b3ca'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Key m:type="Edm.String">NumberOfDimensions</d:Key>
                <d:Value m:type="Edm.String">20</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="63-get-model-sample"></a>6.3。 取得模型範例
取得建議模型的範例。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetModelSample?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`<br><br>特定建立識別碼︰<br>`<rootURI>/GetModelSample?modelId=%27<model_id>%27&buildId=%27<build_id>%27&apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/GetModelSample?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&buildId=%271500068%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

OData XML

原始的文字格式傳回回應︰

<pre>
第 1 層---655 fc 955-a5a3-4a26-9723-3090859cb27b，獵物︰ 嶄新 655 fc 955-a5a3-4a26-9723-3090859cb27b，獵物︰ 嶄新的評等︰ 0.5215 3f471802-f84f-44a0-99 c 8-6d2e7418eec1，向下黑色鷹︰ 現代馬評等的本文︰ 0.5151 07b10e28-9e7c-4032-90b7-10acab7f2460，Cryptonomicon 評等︰ 0.5148 6afc18e4 8c2a-43 維 1-9021-57543d6b11d8，Imajica 評等︰ 0.5146 e4cc5e69-3567-43ab-b00f-f0d8d0506870，按下清單評等︰ 0.514 56b61441-0eed-46cc-a8f6-112775b81892，與死亡中上海 56b61441-0eed-46cc-a8f6-112775b81892，與死亡中上海評等︰ 0.5218 53156702-cc0c-443d-b718-6fb74b2491d3，之 \ 評等︰ 0.5212 fb8cf7a6-8719-46ee-97 d 4-92f931d77a3a，煙霧和鏡像︰ 簡短 Fictions 和 Illusions 評等: 0.5188 8f5fe006-79e4-4679-816b-950989d1db4b 的位置我從未已經 （新型美國小說） 評等︰ 0.5156 d8db4583-cc0f-49ce-bc95-b7fa3491623f，快樂︰ 嶄新的評等︰ 0.5156 50471eec 9aeb-4900 84 d 7-21567ab18546，如果 Buddha 年︰ 在宗教風格的路徑 cfe922a1-7ca0-4f8d-ad9d-b7cc87bfe0ef，尋找愛手冊聖你的機密資料-不對 Sisterhood︰ 嶄新的評等︰ 0.5266 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，Poisonwood bible 》: 嶄新的評等︰ 0.5252 973f8cbd-0846年-4f6b-9 d 28-4dd0d7dc3a19，豬中豎起評等︰ 0.5244 e2cbf7ad-0636年-4117-8b30-298da6df7077、 動物夢想評等: 0.5227 6c818fd3-5a09-417 d-9ab4-7ffe090f0fef，傳統守舊者的醜陋 Stepsister︰ 嶄新的評等︰ 0.5222 5e97148f-defb-4 個 d 74-af2d-80f4763bf531，海洋 （Oprah 的活頁簿俱樂部） 5e97148f-defb-4 個 d 74-af2d-80f4763bf531，海洋 （Oprah 的活頁簿俱樂部） 評等的深結尾的深結尾︰ 0.537 5dcbac37-2946年-4f2a-a0b3-bbe710f9409a，設定島︰ 嶄新的評等︰ 0.5277 bc5b69db-733b-4346-adde-3927544258f7、 Downtown 評等︰ 0.5275 31fe5c63 3e5a-48 維 0-802b-d3b0f989a634，有一個好日︰ 並 Sweatsocks 評等的故事︰ 0.5252 0adf981a b65b-4 a-c 11-b36b-78aca2f948a2，完美大量: True 本文的男人針對海評等︰ 0.5238 68f97068-ae1a-4163-9e94-396b800b743d，Modoc:，則為 True 的最大大象手邊 68f97068-ae1a-4163-9e94-396b800b743d，Modoc 的故事:，則為 True 的最大大象手邊評等的故事︰ 0.5379 6724862e-e4e7-4022-9614-1468d8b902ff，小房子牧場評等︰ 0.5345 cdedb837-1620年-496 d-94 c 4-6ccfed888320，小家中顯示較大的森林評等︰ 0.5325 382164ba-406b-4187-b726-d7a54b9d790d，點選的 Pooh 評等︰ 0.5309 6a068d6a-bb74-4ba3-b3f2-a956c4f9d1b5一鉛色系溪評等︰ 0.5285 37ef8e74-e348-44e5-aabc-1d7f9efcb25b，從火星女人的男人都是從金星︰ 實用指南改善通訊，什麼您想要在您關聯 37ef8e74-e348-44e5-aabc-1d7f9efcb25b，男人是從火星，女人從金星︰ 實用指南改善通訊，什麼您想要在您評等的關聯性︰ 0.5397 f2be16d4-5faf-4 個 d 32-ab83-7ba74d29261e，假期睡覺時間故事︰ 我們生活和時間評等現代化的故事︰ 0.5207 ef732c5c-334b-4d6b-ab82-7255eb7286d0，舉辦之間竊賊評等︰ 0.5195 0b209b8c-7cdd-47fd-b940-05c7ff7c60fc，提供樹狀評等︰ 0.5194 883b360f-8b42-407f-b977-2f44ad840877、 嚇人故事中深告知︰ 收集從美國 Folklore （嚇人故事） 評等︰ 0.5184 ff51b67e-fa8e-4c5e-8f4d-02a928de735d，在公司的男人︰ 棒球 d008dae9-c73a-40a1-9a9b-96d5cf546f36 的特殊功能、 集中營群島 1918年 1956年︰ 實驗中書面調查我-II 評等︰ 0.5416 ff51b67e-fa8e-4c5e-8f4d-02a928de735d，在公司的男人︰ 棒球評等的特殊功能︰ 0.5403 49dec30e-0adb-411a-b186-48eaabf6f8bc，Fatherland 評等︰ 0.5394 cc7964fd-d30f-478e-a425-93ddbdf094ed，個神奇收集︰ 競技場 Vol.1 評等︰ 0.5379 8a1e9f36-97af-4614-bed9-24e3940a05f3，更多 Sniglets︰ 不會顯示在字典中，但是應該評等的文字︰ 0.5377 12a6d988-be21-4a09-8143-9d5f4261ba16，答對的 Eagles 07b10e28-9e7c-4032-90b7-10acab7f2460，Cryptonomicon 評等︰ 0.5417 e4cc5e69-3567-43ab-b00f-f0d8d0506870，按下清單評等︰ 0.5416 1f1a34c4-9781-49f5-a3cc-acec3ae3c71d，家庭評等︰ 0.5371 56daeffe-7 d 48-43 cd-8ef8-7dffd0c103d3，輪替課程評等︰ 0.5366 b2fe511e-5cb9-4a56-b823-2801e63e6a96、 法律應該評等: 0.5366 df87525b-e435-4bd6-8701-4e60ad344e28，找到捕魚 56 d 33036-dfda-46b9-8e2a-76cb03921bb0，X 檔案︰ 基本零評等︰ 0.5417 0780cde8-6529-4e1d-b6c6-082c1b80e596，十二個待紅色 Herrings 評等︰ 0.5416 df87525b-e435-4bd6-8701-4e60ad344e28，找到魚評等︰ 0.5408 400fe331-2 c 35-490 c-adbc-b28b4b73d56c，應該我們判斷總統嗎？ 評等︰ 0.5383 f86ad7d0-5 c 03-42b3-aebf-13d44aec8b30，灰階寬限期評等︰ 0.5358 de1f62a4 89e6-44 維 2-aaee-992a4bf093f1，變更世界地圖︰ 威廉 Smith 和新式地質 de1f62a4 89e6-44 維 2-aaee-992a4bf093f1，變更世界各地的地圖的出生︰ 威廉 Smith 和新式地質評等的出生︰ 0.5422 b303538f-e2c6-4a2c-b425-8d21e684fc3e，評等我得以 Oswald: 0.5385 34b84627 48af-4a4c-96 a-c 4-b26fb3863f56，在 [園藝的建議和惡意評等午夜︰ 0.5379 306cbaa7 b1a8 4142-9 d 55-e11b5018a7a8，街道 Lawyer 評等: 0.5376 e53b4baa-8 c 09 45 c 4 95 c 0-b6a26b98770b，遺漏 Smillas 想要省點的 Snow 評等︰ 0.5367

<a name="level-2"></a>階層 2
---------------
352aaea1-6b12-454 d-a3d5-46379d9e4eb2、 邪惡豬 （Hillerman 良） 352aaea1-6b12-454 d-a3d5-46379d9e4eb2，邪惡豬 （Hillerman 良） 評等︰ 0.5425 74 c 49398-bc10-4af5-a658-a996a1201254，子系大量 （Peters 伊莉莎白） 的評等︰ 0.5387 9ba80080-196e-43fd-8025-391d963f77e7，浮動弄瓦評等︰ 0.5372 e68f81d5-7745-4cc7-b943-fedb8fcc2ced、 殺人笑臉 (Scottoline Lisa) 評等︰ 0.5353 b2fe511e-5cb9-4a56-b823-2801e63e6a96，法律 Tender 評等︰ 0.5332 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5、 湖 Wobegon 天 0adf981a b65b-4 a-c 11-b36b-78aca2f948a2，完美大量︰ True 本文的男人針對海評等︰ 0.5433 c65c3995-abf7-4c7b-bb3c-8eb5aa9be7a5，湖 Wobegon 天評等: 0.543 a00ae6ad-4a7f-4211-9836-75ce8834eb11，Sniglets (Snig'lit︰ 沒有出現在字典中的文字應該而) 評等︰ 0.5327 6f6e192e 和 0 d 64-49ca-9b63-f09413ea1ee6，假期的假日故事︰ 開明聖誕季節的季節評等︰ 0.5307 798051a8-147 d-4 個 d 46-b0dc-e836325029e6，INNOCENCE （影片連接） 評等的年齡︰ 0.5301 73f3e25a-e996-4162-9ed8-ff3d34075650，O Pioneers ！ （企鵝二十世紀中葉經典房車） cba8163f-6536-436b-8130-47b4a43c827f，信任沒有人 （X 檔案 Vol.2 正式指南） 評等︰ 0.5434 5708e4cb-2492年-49 c 0-94a8-cc413eec5d89，評等的小型啊 （Discworld 小說 (Paperback)）︰ 0.5406 73f3e25a-e996-4162-9ed8-ff3d34075650，O Pioneers ！ （企鵝二十世紀中葉經典房車）評等︰ 0.5403 d885b0bd-ae4b-452d-bdf2-faa90197dbc9，神奇評等的色彩︰ 0.539 b133a9c4-4784-4db3-b100-d0d6dffb94d2，藍圖是取出該處 （X 檔案 Vol.1 正式指南） 評等︰ 0.5367 271700a5-854a-4d5a-8409-6b57a5ee4de4，已︰ 或知道為什麼飛翼的鯨魚 Sings 271700a5-854a-4d5a-8409-6b57a5ee4de4，已︰ 或知道為什麼飛翼的鯨魚 Sings 評等︰ 0.5445 2de1c354 90ff-47 a-c 5-a0db-1bad7d88ef94，Salaryman 妻子 （暴力數列的子系） 評等︰ 0.5329 d279416e-19 c 0-43f8-9ec9-a585947879ca，Zen 態度評等: 0.5316 c8f854d7-3de3-4b23-8217-f4f851670fd4，復仇的 Cootie 女生︰ Robin Hudson 神秘 （Robin Hudson 面紗 (Paperback)） 評等︰ 0.5305 8ef4751c-7074-409e-a3ac-d49b222fc864，會在評等野生的項目︰ 0.5289 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1，其 Eyes 已觀看神 9ad1b620-0a7b-4543-8673-66d4c3bcb2f1、 其 Eyes 已觀看神評等︰ 0.5446 da45c4d5-aba1-413b-a9bd-50df98b1e1d2，評等 Bean 樹︰ 0.5389 65ecbdd1 131 c-40 a-c 3-a3d6-d86ca281377a，小型評等的項目神︰ 0.5387 c78743bf-7947-4a0c-8db7-8a3bfe69ba70，石頭評等的行事曆︰ 0.5355 973f8cbd-0846年-4f6b-9 d 28-4dd0d7dc3a19，在豎起評等的豬: 0.5344 5f17d90a-2604年-4fe8-8977-1a280b9098b1，一個用於金錢 （陳鉛色系小說 (Paperback)） 5f17d90a-2604年-4fe8-8977-1a280b9098b1、 一個金錢 （陳鉛色系小說 (Paperback)） 評等︰ 0.5446 57169b2b-9a8a-486b-9aac-1ed98ce57168、 最終的訴求評等︰ 0.5332 efcb1bc4-7278-4a8f-b491-befde02070d6，時間的真實評等︰ 0.5329 1efa91a2 993b-4 a-c 43-9f5c-3454fc12612d，燒錄因子變異數評等︰ 0.5309 24 c 59962-458a-4ec8-b95d-d694e861919c，在家中 Mitford （Mitford 年） 評等︰ 0.5303 4fd48c46 1a20-4 a-c 57-bc7f-a02ef123dc52，以自然做他︰ 引發弄瓦 4fd48c46-1a20-4c57-bc7f-a02ef123dc52 為孩子自然他做︰ 孩子誰已乘冪為弄瓦評等︰ 0.5449 cd5f2c03-20cb-43be-a1fb-3b4233e63222，在 [豎起評等的豬︰ 0.5329 19985fdb-d07a-4a25-ae4a-97b9cb61e5d1，愛中時間 Cholera （企鵝好活頁簿的 20 世紀） 評等︰ 0.5267 15689 d 09 c711-4844 84 d 8-130a90237b26，Bel Canto 評等︰ 0.5245 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，Poisonwood bible 》: 小說評等︰ 0.5235 98df28ec-41e7-4fca-b77f-8b0d3109085d、 難題記憶 f874b5a3-5 d 40-4436-94ff-0fa1c090ddf5，Sun 也讓 （Scribner 傳統） 評等: 0.5451 98df28ec-41e7-4fca-b77f-8b0d3109085d，難題記憶評等︰ 0.5442 0ce0014a-9a48-4013-a08a-7f2c11877930，H.M.S. 看不見的評等︰ 0.5421 15316ca6-1e38-425f-893d-691944a47000、 更多嚇人故事告知中深色的評等︰ 0.5409 329 d 5682-3dc3-4206-8aa2-eef4b1032258，從地球評等的字母︰ 0.54 5b9445d5-c072-419 c-8 d 49-6f669bb1b0a9 的星︰ 小說 （Oprah 的活頁簿俱樂部 (Hardcover)） 5b9445d5-c072-419 c-8 d 49-6f669bb1b0a9 的星︰ 小說 （Oprah 的活頁簿俱樂部 (Hardcover)） 評等︰ 0.5462 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，Poisonwood bible 》: 嶄新的評等︰ 0.5372 604eb3bd-6026-4f51-bffd-9fb54f180400、 家庭圖片︰ 嶄新的評等︰ 0.5341 8d06d01d-31 cd-4678-b6b1-140a67987ce9，一般時間 （Oprah 的活頁簿俱樂部 (Paperback)） 評等的歌曲: 0.5334 da45c4d5-aba1-413b-a9bd-50df98b1e1d2，Bean 樹狀目錄評等︰ 0.5319 d5358189-d70f-4e35-8add-34b83b4942b3，在豎起 d5358189-d70f-4e35-8add-34b83b4942b3 豬、 豬中豎起評等︰ 0.5491 ff91a483-1ce5-4b37-a6fd-5ffcf21f8745，Poisonwood bible 》: 嶄新的評等︰ 0.5401 c78743bf-7947-4a0c-8db7-8a3bfe69ba70，石頭評等的行事曆︰ 0.5393 8d06d01d-31 cd-4678-b6b1-140a67987ce9，一般時間 （Oprah 的活頁簿俱樂部 (Paperback)） 評等的歌曲︰ 0.5382 973f8cbd-0846年-4f6b-9 d 28-4dd0d7dc3a19，豬中豎起評等︰ 0.5367

</pre>


##<a name="7-model-business-rules"></a>7.模型商務規則

以下是支援的規則的類型︰
- <strong>封鎖</strong>封鎖可讓您提供您不想在建議結果中傳回的項目清單。 

- <strong>FeatureBlockList</strong>功能封鎖可讓您要封鎖其功能的數值為根據的項目。

*不會以單一封鎖規則傳送 1000 個以上的項目，或您的來電可能會出現逾時。如果您要封鎖 1000 個以上的項目，您可以打電話數個封鎖。*

- <strong>Upsale</strong> Upsale 可讓您強制執行以傳回建議結果中的項目。

- <strong>WhiteList</strong>白色清單可讓您只建議建議清單中的項目。

- <strong>FeatureWhiteList</strong>功能白色清單可讓您只建議特定功能值的項目。

- <strong>PerSeedBlockList</strong>每個種子封鎖清單可讓您提供每個項目無法建議結果傳回的項目清單。




###<a name="71-get-model-rules"></a>7.1。 取得模型規則

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetModelRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br>範例︰<br>`<rootURI>/GetModelRules?modelId=%271cac7b76-def4-41f1-bc81-29b806adb1de%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

- `feed/entry/content/properties/Id`此規則的唯一識別碼。
- `feed/entry/content/properties/Type`-規則類型。
- `feed/entry/content/properties/Parameter`規則參數。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get a list of rules for a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T12:58:57Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000043</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1000"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfRulesForAModelEntity</title>
        <updated>2014-11-05T12:58:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelRules?modelId='5e824626-50d3-469d-a824-564d38453103'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000044</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1001"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="72-add-rule"></a>7.2。 新增規則

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/AddRule?apiVersion=%271.0%27`|
|頁首   |`"Content-Type", "text/xml"`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 
<ins>每當提供商務規則中的項目識別碼，請務必使用外部識別碼的項目 （在 [類別目錄檔案中所使用的相同識別碼）</ins><br>
<ins>若要新增的封鎖清單規則︰</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>BlockList</Type><Value>{"ItemsToExclude":["2406E770-769C-4189-89DE-1C9283F93A96","3906E110-769C-4189-89DE-1C9283F98888"]}</Value></ApiFilter>`<br><br><ins>
<ins>若要新增 FeatureBlockList 規則︰</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureBlockList</Type><Value>{"Name":"Movie_category","Values":["Adult","Drama"]}</Value></ApiFilter>`<br><br><ins>若要新增 Upsale 規則︰</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>Upsale</Type><Value>{"ItemsToUpsale":["2406E770-769C-4189-89DE-1C9283F93A96"],"NumberOfItemsToUpsale":5}</Value></ApiFilter>`<br><br>
<ins>若要新增 WhiteList 規則︰</ins><br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>WhiteList</Type><Value>{"ItemsToInclude":["2406E770-769C-4189-89DE-1C9283F93A96","1116E770-769C-4189-89DE-1C9283F88888"]}</Value></ApiFilter>`<br><br><ins>
<ins>若要新增 FeatureWhiteList 規則︰</ins><br>
<br>
`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>FeatureWhiteList</Type><Value>{"Name":"Movie_rating","Values":["PG13"]}</Value></ApiFilter>`<br><br><ins>若要新增 PerSeedBlockList 規則︰</ins><br>`<ApiFilter xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"><ModelId>24024f7e-b45c-419e-bfa2-dfd947e0d253</ModelId><Type>PerSeedBlockList</Type><Value>{"SeedItems":["9949"],"ItemsToExclude":["9862","8158","8244"]}</Value></ApiFilter>`|


**回應**︰

HTTP 狀態碼︰ 200

API 傳回剛建立的規則與詳細資料。 可以從下列路徑擷取 [規則] 屬性︰

- `feed/entry/content/properties/Id`此規則的唯一識別碼。
- `feed/entry/content/properties/Type`規則的類型︰ 封鎖或 Upsale。
- `feed/entry/content/properties/Parameter`規則參數。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add A Rule</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-05T11:13:28Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AddARuleEntity</title>
        <updated>2014-11-05T11:13:28Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/AddRule?apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">1000041</d:Id>
                <d:Type m:type="Edm.String">BlockList</d:Type>
                <d:Parameters m:type="Edm.String">{"ItemsToExclude":["1002"]}</d:Parameters>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="73-delete-rule"></a>7.3。 刪除規則

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteRule?modelId=%27<model_id>%27&filterId=%27<filter_Id>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`DeleteRule?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&filterId=%271000011%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   filterId    |   篩選器的唯一識別碼 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

###<a name="74-delete-all-rules"></a>7.4。 刪除所有規則

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteAllRules?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`DeleteAllRules?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

##<a name="8-catalog"></a>8。 目錄

###<a name="81-import-catalog-data"></a>8.1。 匯入目錄資料

如果您將數個目錄檔案上傳到相同的模型有多個電話時，我們會插入只有新的類別目錄項目。 仍會維持原始值的現有的項目。 您無法使用此方法更新目錄資料。

目錄資料應該先按照下列格式︰

- 沒有功能-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- 使用功能-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

附註︰ 檔案大小上限為 200 MB。

**格式的詳細資料**

| 名稱 | 強制性 | 類型 |  描述 |
|:---|:---|:---|:---|
| 項目識別碼 |[是] | [A-z]、 [a-z]、 [0-9]，[_] & #40;底線 & #41;，[-] & #40; 虛線與 #41;<br> 最大長度︰ 50 | 唯一的項目識別碼。 |
| 項目名稱 | [是] | 任何英數字元<br> 最大長度︰ 255 | 項目名稱。 | 
| 項目分類 | [是] | 任何英數字元 <br> 最大長度︰ 255 | 此項目所屬分類 （例如烹飪書籍，齣...）。可以是空的。 |
| 描述 | 否，除非功能發表 （，但可以是空白） | 任何英數字元 <br> 最大長度︰ 4000 | 描述此項目。 |
| 功能清單 | 無 | 任何英數字元 <br> 最大長度︰ 4000;最大的數字的功能︰ 20 | 逗點分隔清單功能名稱 = 功能值，可用來強化模型建議。請參閱[進階主題](#2-advanced-topics)一節。 |


| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/ImportCatalogFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ImportCatalogFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27catalog10_small.txt%27&apiVersion=%271.0%27`|
|頁首   |`"Content-Type", "text/xml"`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼  |
| 檔案名稱 | 文字的目錄識別碼。<br>僅限字母 A 至 Z （a 到 z），數字 (0-9)，將連字號 （-），並允許底線 (_)。<br>最大長度︰ 50 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | （包含功能） 的範例︰<br/>2406e770-769 c-4189-89de-1c9283f93a96，塔克拉拉 Callan，活頁簿，活頁簿描述，作者 = 翰工作 Wright publisher = Harper Flamingo 加拿大，年 = 2001年<br>21bf8088-b6c0-4509-870 c-e1c7ac78304a，忘記聊天室︰ 小說 （拜占庭活頁簿）、 活頁簿、 作者 = Nick Bantock publisher = Harpercollins，年 = 1997年<br>3bb5cb44-d143-4bdd-a55c-443964bf4b23，Spadework、 活頁簿、 作者 = Timothy Findley publisher = HarperFlamingo 加拿大年 = 2001年<br>552a1940-21e4-4399-82bb-594b46d7ed54，我們必須把這些本例、 活頁簿，活頁簿描述，作者 = Magnus 廠 publisher = 限時障礙發佈年 = 1998年</pre> |


**回應**︰

HTTP 狀態碼︰ 200

API 傳回匯入的報表。
- `feed\entry\content\properties\LineCount`的接受行數。
- `feed\entry\content\properties\ErrorCount`-不插入發生錯誤的行數。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Import catalog file</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T06:58:04Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ImportCatalogFileEntity2</title>
        <updated>2014-10-05T06:58:04Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportCatalogFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='catalog10_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:LineCount m:type="Edm.String">4</d:LineCount>
                <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="82-get-catalog"></a>8.2。 取得目錄
擷取所有目錄項目。
目錄會擷取一頁，一次。 如果您想要取得的特定索引的項目，您可以使用 $skip odata 參數。 舉個例說如果您想要取得的位置開始 100 的項目，新增參數 $skip = 100 邀請。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetCatalog?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`GetCatalog?modelId=%2724024f7e-b45c-419e-bfa2-dfd947e0d253%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

回應包含一個項目，每個目錄項目。 每個項目具有下列資料︰

- `feed/entry/content/properties/ExternalId`-目錄項目外部識別碼，客戶所提供的項目。
- `feed/entry/content/properties/InternalId`-目錄項目內部識別碼，產生 Azure 電腦學習建議的項目。
- `feed/entry/content/properties/Name`目錄項目名稱。
- `feed/entry/content/properties/Category`目錄項目分類。
- `feed/entry/content/properties/Description`-目錄項目描述。
- `feed/entry/content/properties/Metadata`-目錄項目中繼資料。


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get All Catalog Items</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">552A1940-21E4-4399-82BB-594B46D7ED54</d:ExternalId>
                <d:InternalId m:type="Edm.String">060db26a-e6a6-464e-bb52-436d2da82a50</d:InternalId>
                <d:Name m:type="Edm.String">Restraint of Beasts</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:ExternalId>
                <d:InternalId m:type="Edm.String">209d7bfe-2eb9-4455-92a3-7c867a41a74a</d:InternalId>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">3BB5CB44-D143-4BDD-A55C-443964BF4B23</d:ExternalId>
                <d:InternalId m:type="Edm.String">913ff79b-059b-4d4d-8042-6fa4cc1391dd</d:InternalId>
                <d:Name m:type="Edm.String">Spadework</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">AllCatalogItemsEntity</title>
        <updated>2014-10-29T11:13:26Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalog?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:ExternalId m:type="Edm.String">21BF8088-B6C0-4509-870C-E1C7AC78304A</d:ExternalId>
                <d:InternalId m:type="Edm.String">ea65e4fa-768c-40b4-92c3-69d3e8178691</d:InternalId>
                <d:Name m:type="Edm.String">The Forgetting Room: A Fiction (Byzantium Book)</d:Name>
                <d:Category m:type="Edm.String">Book</d:Category>
                <d:Description m:type="Edm.String"></d:Description>
                <d:Metadata m:type="Edm.String"></d:Metadata>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="83-get-catalog-items-by-token"></a>8.3。 取得權杖目錄項目

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetCatalogItemsByToken?modelId=%27<modelId>%27&token=%27<token>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`GetCatalogItemsByToken?modelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&token=%27Cla%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼 |
|   權杖   |   目錄項目的名稱的 token。 包含至少有 3 個字元。 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

回應包含一個項目，每個目錄項目。 每個項目具有下列資料︰

- `feed/entry/content/properties/InternalId`-目錄項目內部識別碼，產生 Azure 電腦學習建議的項目。
- `feed/entry/content/properties/Name`目錄項目名稱。
- `feed/entry/content/properties/Rating`-（適用於供日後使用）
- `feed/entry/content/properties/Reasoning`-（適用於供日後使用）
- `feed/entry/content/properties/Metadata`-（適用於供日後使用）
- `feed/entry/content/properties/FormattedRating`-（適用於供日後使用）

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get Catalog items that contain a token</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-29T11:48:19Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">CatalogItemsThatContainATokenEntity</title>
            <updated>2014-10-29T11:48:19Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetCatalogItemsByToken?modelId='0dbb55fa-7f11-418d-8537-8ff2d9d1d9c6'&amp;token='Cla'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                    <d:Name m:type="Edm.String">Clara Callan</d:Name>
                    <d:Rating m:type="Edm.Double">0</d:Rating>
                    <d:Reasoning m:type="Edm.String"></d:Reasoning>
                    <d:Metadata m:type="Edm.String"></d:Metadata>
                    <d:FormattedRating m:type="Edm.Double" m:null="true"></d:FormattedRating>
                </m:properties>
            </content>
        </entry>
    </feed>

##<a name="9-usage-data"></a>9。 使用資料
###<a name="91-import-usage-data"></a>9.1。 匯入使用資料
####<a name="911-uploading-file"></a>9.1.1。 上傳的檔案
本節說明如何使用檔案上傳使用資料。 您可以呼叫此 API 數次與使用情況資料。 所有的使用情況資料會儲存所有通話。

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/ImportUsageFile?modelId=%27<modelId>%27&filename=%27<fileName>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ImportUsageFile?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&filename=%27ImplicitMatrix10_Guid_small.txt%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId |   模型的唯一識別碼  |
| 檔案名稱 | 文字的目錄識別碼。<br>僅限字母 A 至 Z （a 到 z），數字 (0-9)，將連字號 （-），並允許底線 (_)。<br>最大長度︰ 50 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 使用資料。 格式︰<br>`<User Id>,<Item Id>[,<Time>,<Event>]`<br><br><table><tr><th>名稱</th><th>強制性</th><th>類型</th><th>描述</th></tr><tr><td>使用者識別碼</td><td>[是]</td><td>[A-z]、 [a-z]、 [0-9]，[_] & #40;底線 & #41;，[-] & #40; 虛線與 #41;<br> 最大長度︰ 255 </td><td>使用者的唯一識別碼。</td></tr><tr><td>項目識別碼</td><td>[是]</td><td>[A-z]、 [a-z]、 [0-9]，[& #95;] & #40;底線 & #41;，[-] & #40; 虛線與 #41;<br> 最大長度︰ 50</td><td>唯一的項目識別碼。</td></tr><tr><td>時間</td><td>無</td><td>日期格式︰ YYYY/MM/DDTHH:MM:SS (例如 2013年/06/20T10:00:00)</td><td>資料的時間。</td></tr><tr><td>事件</td><td>否;如果提供然後也必須將日期</td><td>下列其中一項︰<br>• 按一下<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• 購買</td><td></td></tr></table><br>檔案大小上限︰ 200 MB<br><br>範例︰<br><pre>149452，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>6360，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>50321，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>71285，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>224450，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>236645，1b3d95e2-84e4-414 c-bb38-be9cf461c347<br>107951，1b3d95e2-84e4-414 c-bb38-be9cf461c347</pre> |

**回應**︰

HTTP 狀態碼︰ 200

- `Feed\entry\content\properties\LineCount`的接受行數。
- `Feed\entry\content\properties\ErrorCount`-不插入發生錯誤的行數。
- `Feed\entry\content\properties\FileId`-檔案識別碼。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Add bulk usage data (usage file)</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">AddBulkUsageDataUsageFileEntity2</title>
    <updated>2014-10-05T07:21:44Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ImportUsageFile?modelId='a658c626-2baa-43a7-ac98-f6ee26120a12'&amp;filename='ImplicitMatrix10_Guid_small.txt'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:LineCount m:type="Edm.String">33</d:LineCount>
        <d:ErrorCount m:type="Edm.String">0</d:ErrorCount>
        <d:FileId m:type="Edm.String">fead7c1c-db01-46c0-872f-65bcda36025d</d:FileId>
      </m:properties>
    </content>
    </entry>
    </feed>


####<a name="912-using-data-acquisition"></a>9.1.2。 使用資料擷取
本節說明如何事件中傳送即時 Azure 電腦學習建議，通常會從您的網站。

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/AddUsageEvent?apiVersion=%271.0%27`|
|頁首   |`"Content-Type", "text/xml"`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   apiVersion      | 1.0 |
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
                    <PurchaseItem>
                        <ItemId>ABBF8081-C5C0-4F09-9701-E1C7AC78304A</ItemId>
                        <Count>1</Count>
                    </PurchaseItem>
                    <PurchaseItem>
                        <ItemId>21BF8088-B6C0-4509-870C-11C0AC7F304B</ItemId>
                        <Count>3</Count>
                    </PurchaseItem>
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

###<a name="92-list-model-usage-files"></a>9.2。 列出模型使用檔案
擷取所有模型使用檔案的中繼的資料。
檔案會使用一次擷取一頁。 每個頁面包含 100 項目。 如果您想要取得的特定索引的項目，您可以使用 $skip odata 參數。 舉個例說如果您想要取得的位置開始 100 的項目，新增參數 $skip = 100 邀請。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/ListModelUsageFiles?forModelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ListModelUsageFiles?forModelId=%270dbb55fa-7f11-418d-8537-8ff2d9d1d9c6%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   forModelId  |   模型的唯一識別碼 |
|   apiVersion      | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

回應包含每個使用檔案的一個項目。 每個項目具有下列資料︰

- `feed\entry\content\properties\Id`使用檔案。
- `feed\entry\content\properties\Length`使用檔案長度 mb。
- `feed\entry\content\properties\DateModified`使用檔案時建立日期。
- `feed\entry\content\properties\UseInModel`-是否模型中使用使用檔案。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of model's usage files info</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
            <updated>2014-10-30T09:40:25Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Id m:type="Edm.String">4c067b42-e975-4cb2-8c98-a6ab80ed6d63</d:Id>
                    <d:Length m:type="Edm.Double">0</d:Length>
                    <d:DateModified m:type="Edm.String">10/30/2014 9:19:57 AM</d:DateModified>
                    <d:UseInModel m:type="Edm.String">true</d:UseInModel>
                </m:properties>
            </content>
        </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetAListOfModelsUsageFilesInfoEntity</title>
        <updated>2014-10-30T09:40:25Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ListModelUsageFiles?forModelId='1c1110f8-7d9f-4c64-a807-4c9c5329993a'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">3126d816-4e80-4248-8339-1ebbdb9d544d</d:Id>
                <d:Length m:type="Edm.Double">0.001</d:Length>
                <d:DateModified m:type="Edm.String">10/30/2014 9:21:44 AM</d:DateModified>
                <d:UseInModel m:type="Edm.String">true</d:UseInModel>
            </m:properties>
        </content>
    </entry>
</feed>

###<a name="93-get-usage-statistics"></a>9.3。 取得使用統計資料
取得使用統計資料。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetUsageStatistics?modelId=%27<modelId>%27& startDate=%27<date>%27&endDate=%27<date>%27&eventTypes=%27<types>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetUsageStatistics?modelId=%271d20c34f-dca1-4eac-8e5d-f299e4e4ad66%27&startDate=%272014%2F10%2F17T00%3A00%3A00%27&endDate=%272014%2F11%2F16T00%3A00%3A00%27&eventTypes=%271%2C2%2C3%2C4%2C5%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼  |
| 開始日期 |   開始日期。 格式︰ yyyy/MM/ddTHH:mm:ss |
| 結束日期 | 結束日期。 格式︰ yyyy/MM/ddTHH:mm:ss |
| eventTypes |  逗點分隔字串事件類型或 null，以取得所有事件  |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

索引鍵/值項目集合。 每個包含特定事件類型以小時分組的事件的總和。

- `feed\entry[i]\content\properties\Key`-（以小時分組） 的時間和事件類型的內容。
- `feed\entry[i]\content\properties\Value`的事件總字數統計。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get usage statistics</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-11-18T11:39:16Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;Click</d:Event>
                <d:Value m:type="Edm.String">5</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/9/2014 8:00:00 AM;RecommendationClick</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/1/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
        <title type="text">GetUsageStatisticsEntity</title>
        <updated>2014-11-18T11:39:16Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUsageStatistics?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;startDate='2014/10/12T00:00:00'&amp;endDate='2014/11/10T00:00:00'&amp;eventTypes=''&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Event m:type="Edm.String">11/5/2014 8:00:00 AM;RemoveShopCart</d:Event>
                <d:Value m:type="Edm.String">10</d:Value>
            </m:properties>
        </content>
    </entry>
    </feed>

###<a name="94-get-usage-file-sample"></a>9.4。 取得使用檔案的範例
擷取的使用狀況檔案內容第 2 篇知識庫文章。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetUsageFileSample?modelId=%27<modelId>%27& fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetUsageFileSample?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fileId=%274c067b42-e975-4cb2-8c98-a6ab80ed6d63%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼  |
| fileId |  模型使用檔案的唯一識別碼  |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

原始的文字格式傳回回應︰
<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15True、 1 235588，21BF8088-B6C0-4509-870 C-E1C7AC78304A，2014年/11/02T13:40:15，True、 1 158254，21BF8088-B6C0-4509-870 C-E1C7AC78304A，2014年/11/02T13:40:15，True、 1 271195，21BF8088-B6C0-4509-870 C-E1C7AC78304A，2014年/11/02T13:40:15，True、 1 141157，21BF8088-B6C0-4509-870 C-E1C7AC78304A，2014年/11/02T13:40:15，True、 1 171118，3BB5CB44-D143-4BDD-A55C-443964BF4B23，2014年/11/02T13:40:15，True、 1 225087，3BB5CB44-D143-4BDD-A55C-443964BF4B23，2014年/11/02T13:40:15，True、 1
</pre>


###<a name="95-get-model-usage-file"></a>9.5。 取得模型使用檔案
擷取使用檔案的完整內容。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetModelUsageFile?mid=%27<modelId>%27& fid=%27<fileId>%27&download=%27<download_value>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetModelUsageFile?mid=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&fid=%273126d816-4e80-4248-8339-1ebbdb9d544d%27&download=%271%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| mid | 模型的唯一識別碼  |
| fid | 模型使用檔案的唯一識別碼 |
| 下載 | 1 |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

原始的文字格式傳回回應︰
<pre>
85526,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 210926,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 116866,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 177458,2406E770-769C-4189-89DE-1C9283F93A96,2014/11/02T13:40:15,True,1 274004,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 123883,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 37712,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 152249,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 250948,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 235588,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 158254,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 271195,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 141157,21BF8088-B6C0-4509-870C-E1C7AC78304A,2014/11/02T13:40:15,True,1 171118,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 225087,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 244881,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 50547,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 213090,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 260655,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 72214,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 36326,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189336,3BB5CB44-D143-4BDD-A55C-443964BF4B23,2014/11/02T13:40:15,True,1 189334,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 260655,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 162100,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15,True,1 54946,552A1940-21E4-4399-82BB-594B46D7ED54,2014/11/02T13:40:15True、 1 260965，552A1940-21E4-4399-82BB-594B46D7ED54，2014年/11/02T13:40:15，True、 1 102758，552A1940-21E4-4399-82BB-594B46D7ED54，2014年/11/02T13:40:15，True、 1 112602，552A1940-21E4-4399-82BB-594B46D7ED54，2014年/11/02T13:40:15，True、 1 163925，552A1940-21E4-4399-82BB-594B46D7ED54，2014年/11/02T13:40:15，True、 1 262998，552A1940-21E4-4399-82BB-594B46D7ED54，2014年/11/02T13:40:15，True、 1 144717，552A1940-21E4-4399-82BB-594B46D7ED54，2014年/11/02T13:40:15，True、 1
</pre>

###<a name="96-delete-usage-file"></a>9.6。 刪除使用檔案
刪除指定的模型使用檔案。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteUsageFile?modelId=%27<modelId>%27&fileId=%27<fileId>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/DeleteUsageFile?modelId=%270f86d698-d0f4-4406-a684-d13d22c47a73%27&fileId=%27f2e0b09d-be5c-46b2-9ac2-c7f622e5e1a5%27&apiVersion=%271.0%27`|

| 參數名稱    |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼  |
| fileId | 要刪除之檔案的唯一識別碼 |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200


###<a name="97-delete-all-usage-files"></a>9.7。 刪除所有使用檔案
刪除所有模型使用檔案。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteAllUsageFiles?modelId=%27<modelId>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/DeleteAllUsageFiles?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&apiVersion=%271.0%27`|

| 參數名稱    |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼  |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

##<a name="10-features"></a>10.功能
本節說明如何擷取功能的資訊，例如匯入的功能和其值，其排名]，並配置此排名。 屬於目錄資料，匯入功能，且其排名然後有關聯完成等級的建立。
根據的使用狀況]，然後輸入的項目模式，可以變更功能排名。 但一致使用方式/項目，排名應有只小型變動。
排名的功能是非負數數字。 數字 0 表示功能已不排名 （情況呼叫之前的第一個 rank 建立完成此 API）。 排名使用哪些屬性的日期稱為分數新鮮度。

###<a name="101-get-features-info-for-last-rank-build"></a>10.1。 取得功能資訊 （最後一個 Rank 建立）
擷取的功能的資訊，包括排名，最後一個成功 rank 組建。

| HTTP 方法 | URI |
|:--------|:--------|
|取得      |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&apiVersion=%271.0%27`

| 參數名稱    |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼  |
|samplingSize| 根據的資料目錄] 中的每一個功能所包含的值的數目。 <br/>可能的值為︰<br> -1-所有的範例。 <br>0-沒有範例。 <br>N-會傳回 N 的每一個功能名稱的範例。|
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |


**回應**︰

HTTP 狀態碼︰ 200

回應包含功能的資訊項目的清單。 包含每一個項目︰

- `feed/entry/content/m:properties/d:Name`功能名稱。
- `feed/entry/content/m:properties/d:RankUpdateDate`日期的排名配置此功能，又名 成績新鮮度功能。 歷史的日期 ('0001-01-01T00:00:00 」) 表示已執行任何 rank 建置。
- `feed/entry/content/m:properties/d:Rank`功能排名 （浮點數）。 排名 2.0 和設定會被視為良好的功能。
- `feed/entry/content/m:properties/d:SampleValues`-逗點分隔值進位要求的取樣大小至清單。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get the features of a model</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-01-08T13:15:02Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Author</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Name m:type="Edm.String">Publisher</d:Name>
                <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
                <d:Rank m:type="Edm.String">0</d:Rank>
                <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
            </m:properties>
        </content>
    </entry>
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
        <title type="text">ModelFeaturesEntity</title>
        <updated>2015-01-08T13:15:02Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1"/>
        <contenttype="application/xml">
        <m:properties>
            <d:Name m:type="Edm.String">Year</d:Name>
            <d:RankUpdateDate m:type="Edm.String">0001-01-01T00:00:00</d:RankUpdateDate>
            <d:Rank m:type="Edm.String">0</d:Rank>
            <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
        </m:properties>
        </content>
    </entry>
</feed>


###<a name="102-get-features-info-for-specific-rank-build"></a>10.2。 取得功能資訊 （特定 Rank 建立）

擷取的功能的資訊，包括特定的次序建立的次序。

| HTTP 方法 | URI |
|:--------|:--------|
|取得      |`<rootURI>/GetModelFeatures?modelId=%27<modelId>%27&samplingSize=%27<samplingSize>%27&rankBuildId=<rankBuildId>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetModelFeatures?modelId=%271c1110f8-7d9f-4c64-a807-4c9c5329993a%27&samplingSize=10%27&rankBuildId=1000551&apiVersion=%271.0%27`

| 參數名稱    |   有效的值    |
|:--------          |:--------          |
| modelId | 模型的唯一識別碼  |
|samplingSize| 根據的資料目錄] 中的每一個功能所包含的值的數目。<br/> 可能的值為︰<br> -1-所有的範例。 <br>0-沒有範例。 <br>N-會傳回 N 的每一個功能名稱的範例。|
|rankBuildId| Rank 建置] 或 [最後一個 rank 組建-1 的唯一識別碼|
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |


**回應**︰

HTTP 狀態碼︰ 200

回應包含功能的資訊項目的清單。 包含每一個項目︰

- `feed/entry/content/m:properties/d:Name`功能名稱。
- `feed/entry/content/m:properties/d:RankUpdateDate`日期的排名配置此功能，又名 成績新鮮度功能。 歷史的日期 ('0001-01-01T00:00:00 」) 表示已執行任何 rank 建置。
- `feed/entry/content/m:properties/d:Rank`功能排名 （浮點數）。 排名 2.0 和設定會被視為良好的功能。
- `feed/entry/content/m:properties/d:SampleValues`-逗點分隔值進位要求的取樣大小至清單。

OData

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get the features of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:54:22Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Author</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">3.38867426</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. A. Attanasio, A. A. Milne, A. Bates, A. C. Bhaktivedanta Swami Prabhupada et al., A. C. Crispin, A. C. Doyle, A. C. H. Smith, A. E. Parker, A. J. Holt, A. J. Matthews</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Publisher</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.67839336</d:Rank>
                    <d:SampleValues m:type="Edm.String">A. Mondadori, Abacus, Abacus Press, Abacus Uk, Abstract Studio, Acacia Press, Academy Chicago Publishers, Ace Books, ACE Charter, Actar</d:SampleValues>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">ModelFeaturesEntity</title>
            <updated>2015-01-08T13:54:22Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelFeatures?modelId='f13ab2e8-b530-4aa1-86f7-2f4a24714765'&amp;samplingSize='10'&amp;rankBuildId=1000653&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Name m:type="Edm.String">Year</d:Name>
                    <d:RankUpdateDate m:type="Edm.String">2015-01-08T13:52:14.673</d:RankUpdateDate>
                    <d:Rank m:type="Edm.String">1.12352145</d:Rank>
                    <d:SampleValues m:type="Edm.String">0, 1920, 1926, 1927, 1929, 1930, 1932, 1942, 1943, 1946</d:SampleValues>
                </m:properties>
            </content>
        </entry>
    </feed>


##<a name="11-build"></a>11。 建立

  本節說明的不同的 Api 相關建置。 有 3 種建置︰ 建議建立等級的建立與 FBT （一起經常購買） 建立。

建議建立的目的是要產生建議模型用於預測。 預測 （適用於建立此類型） 會以兩種類別︰
* I2I-又名 項目至項目建議-項目或此選項會預測的可能是高感興趣的項目清單的項目清單。
* U2I-又名 項目建議-指定的使用者識別碼 （和 （選擇性） 的項目清單） 的使用者這個選項會預測的可能是指定的使用者 （和其其他選擇的項目） 高感興趣的項目清單。 U2I 建議為基礎的項目已建立模型的時間使用者感興趣的歷程記錄。

Rank 建置為技術建置，可讓您瞭解您的功能的用途。 通常，若要取得最佳結果，建議模型相關功能，您應該採取下列步驟︰
- 觸發程序排名建立 （除非您功能的成績都會很穩定） 和等候，直到您取得功能分數。
- 擷取您功能的排名，則可電話[取得功能資訊](#101-get-features-info-for-last-rank-build)API。
- 設定建議建立下列參數︰
    - `useFeatureInModel`-設為 True。
    - `ModelingFeatureList`-設定為 [逗點分隔的分數 2.0 或更多 （根據您在上一個步驟中擷取以） 的功能清單。
    - `AllowColdItemPlacement`-設為 True。
    - 您可以選擇設定`EnableFeatureCorrelation`為 True，`ReasoningFeatureList`您要使用的說明 （通常是相同的功能清單用於 modelling 或子清單） 的功能清單。
- 設定參數的建議建立觸發程序。

注意︰ 如果您沒有設定任何參數 （例如叫用參數不建議建立） 或您不明確停用功能的使用方式 (例如`UseFeatureInModel`設定為 False)，系統會設定功能相關說明所示的值上述參數以防 rank 建置存在。

沒有任何限制上執行 rank 建置和建議建置同時的相同的模型。 不過，您無法執行相同的模型平行相同類型的兩個建置。

FBT （一起經常購買） 建立尚稱為有時 」 傳統的 「 recommender，這是很好的不是同類型的類別目錄的其他建議演算法 (同質性︰ 活頁簿、 影片、 一些食物，以; 非同類型︰ 電腦和裝置、 跨網域，各種)。

注意︰ 如果您上傳的使用狀況檔案包含選用的功能變數 「 事件類型 」 然後 FBT modelling 只 」 購買 「 事件適用。 如果沒有事件類型會提供所有事件會被都視為購買。


####<a name="111-build-parameters"></a>11.1 建立參數

透過一組參數 （如下所示），您可以設定每一個建立的類型。 如果您沒有設定參數，系統會自動根據呈現的資訊同時觸發建立參數屬性值。

#####<a name="1111-usage-condenser"></a>11.1.1。 使用冷凝器
使用者或幾個使用點的項目可能包含更多雜音比資訊。 系統會嘗試預測每個使用者/項目，以在模型中使用的使用狀況點的最小的數字。 這會是內所定義的項目、 ItemCutoffLowerBound 和 ItemCutoffUpperBound 參數的範圍，以 UserCutOffLowerBound 和 UserCutoffUpperBound 參數的使用者定義的範圍。 冷凝器效果的項目或使用者可以最小化設定至少一個對應的範圍為零。

#####<a name="1112-rank-build-parameters"></a>11.1.2。 排名建立參數

下表描述建立參數的等級的建立。

|索引鍵|描述|類型|有效的值|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 模型執行重複次數會反映整體計算時間，以及模型精確度。 較高的數字、 較佳的精確度就會出現，但計算時間會採取更長的時間。| 整數 | 10-50 |
| NumberOfModelDimensions | 模型會嘗試尋找您的資料中的 「 功能 」 的數量與維度的數目。 增加維度的數目，可讓更有效地將較小的叢集微調的結果。 不過，太多維度會防止模型尋找項目之間的關聯性。 | 整數 | 10 40 |
|ItemCutOffLowerBound| 定義冷凝器項目下限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|ItemCutOffUpperBound| 定義冷凝器項目上限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|UserCutOffLowerBound| 定義冷凝器使用者下限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|UserCutOffUpperBound| 定義冷凝器使用者上限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |

#####<a name="1113-recommendation-build-parameters"></a>11.1.3。 建議建立參數
下表描述建議建立建立的參數。

|索引鍵|描述|類型|有效的值|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 模型執行重複次數會反映整體計算時間，以及模型精確度。 較高的數字、 較佳的精確度就會出現，但計算時間會採取更長的時間。| 整數 | 10-50 |
| NumberOfModelDimensions | 模型會嘗試尋找您的資料中的 「 功能 」 的數量與維度的數目。 增加維度的數目，可讓更有效地將較小的叢集微調的結果。 不過，太多維度會防止模型尋找項目之間的關聯性。 | 整數 | 10 40 |
|ItemCutOffLowerBound| 定義冷凝器項目下限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|ItemCutOffUpperBound| 定義冷凝器項目上限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|UserCutOffLowerBound| 定義冷凝器使用者下限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|UserCutOffUpperBound| 定義冷凝器使用者上限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
| 描述 | 建立描述。 | 字串 | 任何文字、 最大 512 個字元 |
| EnableModelingInsights | 可讓您以計算建議模型上的指標。 | 布林值 | True/False |
| UseFeaturesInModel | 指出功能是否可以用來強化建議模型。 | 布林值 | True/False |
| ModelingFeatureList | 逗點分隔功能名稱會在建議建立，用來強化建議的清單。 | 字串 | 功能名稱最多 512 個字元 |
| AllowColdItemPlacement | 指出是否建議也應該推低溫的項目透過功能相似性。 | 布林值 | True/False |
| EnableFeatureCorrelation | 表示是否功能可以使用的原因。 | 布林值 | True/False |
| ReasoningFeatureList | 逗點分隔的用於弄句子 （例如建議說明） 的功能名稱清單。  | 字串 | 功能名稱最多 512 個字元 |
| EnableU2I | 又名允許個人化的建議 U2I （項目建議使用者）。 | 布林值 | True/False (預設為 true) |

#####<a name="1114-fbt-build-parameters"></a>11.1.4。 FBT 建立參數
下表描述建議建立建立的參數。

|索引鍵|描述|類型|有效的值 （預設值）|
|:-----|:----|:----|:---|
|FbtSupportThreshold | 如何傳統是模型。 次數共同要考慮的模型的項目。| 整數 | 3-50 (6) |
|FbtMaxItemSetSize | 範圍經常集中的項目數。| 整數 | 2-3 (2) |
|FbtMinimalScore | 應該有常用的設定，才會包含在傳回的結果的最小分數。 更好。| 點兩下 | 0 和上方 (0) |
|FbtSimilarityFunction | 定義建置所使用的相似性函數。 增益偏袒 serendipity 共同的項目偏袒預測，與 Jaccard 是介於兩個部份洩漏。 | 字串 | cooccurrence 增益，jaccard （增益） |


###<a name="112-trigger-a-recommendation-build"></a>11.2。 觸發程序建議建立

  預設這個 API 會觸發建議模型建立。 若要觸發 rank 建立 （才能分數功能） 中，應使用與建立輸入參數，建立 API 變化。


| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&apiVersion=%271.0%27`|
|頁首   |`"Content-Type", "text/xml"`（如果傳送邀請內文）|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼  |
| userDescription | 文字的目錄識別碼。 請注意，是否您是使用空格您必須編碼，因此以 %20 改為。 請參閱上述範例。<br>最大長度︰ 50 |
| apiVersion | 1.0 |
|||
| 邀請內文 | 如果空白的建立就會執行與預設值建立參數。<br><br>如果您想要設定建立參數，傳送 XML 為參數，如下列範例所示本文。 （請參閱 「 建立參數 」 區段的參數說明）。`<NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance><EnableModelingInsights>true</EnableModelingInsights><UseFeaturesInModel>false</UseFeaturesInModel><ModelingFeatureList>feature_name_1,feature_name_2,...</ModelingFeatureList><AllowColdItemPlacement>false</AllowColdItemPlacement><EnableFeatureCorrelation>false</EnableFeatureCorrelation><ReasoningFeatureList>feature_name_a,feature_name_b,...</ReasoningFeatureList></BuildParametersList>` |

**回應**︰

HTTP 狀態碼︰ 200

這是非同步 API。 您會收到建立識別碼做為回應。 若要知道建立已結束時，請您應該撥打 「 取得建置狀態的模型 」 API，並在回應中找出此建立 ID。 請注意，建立可以從分鐘為根據的資料大小的小時。

您無法使用建議，直到建立]: 結束。

有效建立狀態︰

- 建立-建立建立邀請。
- 佇列-建立要求傳送，以及它佇列。
- 建置-建立連線。
- 成功-建立順利結束。
- 錯誤-建立結束工作流程失敗。
- 取消-建立已取消。
- 取消-建置取消要求傳送。


請注意，建立識別碼可以會在以下路徑︰`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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

###<a name="113-trigger-build-recommendation-rank-or-fbt"></a>11.3。 觸發程序建立 （建議、 排名或 FBT）

| HTTP 方法 | URI |
|:--------|:--------|
|文章     |`<rootURI>/BuildModel?modelId=%27<modelId>%27&userDescription=%27<description>%27&buildType=%27<buildType>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/BuildModel?modelId=%27a658c626-2baa-43a7-ac98-f6ee26120a12%27&userDescription=%27First%20build%27&buildType=%27Ranking%27&apiVersion=%271.0%27`|
|頁首   |`"Content-Type", "text/xml"`（如果傳送邀請內文）|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼  |
| userDescription | 文字的目錄識別碼。 請注意，是否您是使用空格您必須編碼，因此以 %20 改為。 請參閱上述範例。<br>最大長度︰ 50 |
| buildType | 建立叫用類型︰ <br/> -建議組建 」 建議 <br> -'次序' rank 建立 <br/> -FBT 組建 」 Fbt
| apiVersion | 1.0 |
|||
| 邀請內文 | 如果空白的建立就會執行與預設值建立參數。<br><br>如果您想要設定建立參數，傳送給他們為 XML 將在本文中，如下列範例。 （請參閱 [說明及參數的完整清單的 「 建立參數 」 一節）。`<BuildParametersList><NumberOfModelIterations>40</NumberOfModelIterations><NumberOfModelDimensions>20</NumberOfModelDimensions><MinItemAppearance>5</MinItemAppearance><MinUserAppearance>5</MinUserAppearance></BuildParametersList>` |

**回應**︰

HTTP 狀態碼︰ 200

這是非同步 API。 您會收到建立識別碼做為回應。 若要知道建立已結束時，請您應該撥打 「 取得建置狀態的模型 」 API，並在回應中找出此建立 ID。 請注意，建立可以從分鐘為根據的資料大小的小時。

您無法使用建議，直到建立]: 結束。

有效建立狀態︰

- 建立-建立模型。
- 佇列-觸發模型建置以及它佇列。
- 建立建置-模型。
- 成功-建立順利結束。
- 錯誤-建立結束工作流程失敗。
- 取消-建立已取消。
- 取消-建立已取消。

請注意，建立識別碼可以會在以下路徑︰`Feed\entry\content\properties\Id`

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Build a Model with RequestBody</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">BuildAModelEntity2</title>
    <updated>2014-10-05T08:56:34Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/BuildModel?modelId='9559872f-7a53-4076-a3c7-19d9385c1265'&amp;userDescription='First%20build'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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




###<a name="114-get-builds-status-of-a-model"></a>11.4。 取得建立模型的狀態
擷取建置和他們的狀態，指定的模型。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetModelBuildsStatus?modelId=%27<modelId>%27&onlyLastBuild=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetModelBuildsStatus?modelId=%279559872f-7a53-4076-a3c7-19d9385c1265%27&onlyLastBuild=true&apiVersion=%271.0%27`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   modelId         |   模型的唯一識別碼  |
|   onlyLastBuild   |   指出是否要傳回的模型的所有建立歷程記錄] 或 [最新建立的狀態  |
|   apiVersion      |   1.0                                 |


**回應**︰

HTTP 狀態碼︰ 200

回應包含建立每一個項目。 每個項目具有下列資料︰

- `feed/entry/content/properties/UserName`使用者的名稱。
- `feed/entry/content/properties/ModelName`模型的名稱。
- `feed/entry/content/properties/ModelId`-模型的唯一識別碼。
- `feed/entry/content/properties/IsDeployed`-建立是否 （又名部署 作用中建立）。
- `feed/entry/content/properties/BuildId`-建立唯一識別碼。
- `feed/entry/content/properties/BuildType`-輸入的建立]。
- `feed/entry/content/properties/Status`-建立狀態。 可以是下列其中一項︰ 錯誤、 建置、 佇列、 Cancelling、 已取消、 成功。
- `feed/entry/content/properties/StatusMessage`-（僅適用於特定狀態） 詳細的狀態訊息。
- `feed/entry/content/properties/Progress`-建立進度 （%）。
- `feed/entry/content/properties/StartTime`-建立開始時間。
- `feed/entry/content/properties/EndTime`-建立結束時間。
- `feed/entry/content/properties/ExecutionTime`-建立工期。
- `feed/entry/content/properties/ProgressStep`建立進行中的目前階段的相關詳細資料。

有效建立狀態︰
- 建立-建立建立要求輸入。
- 佇列-觸發建立要求，以及它佇列。
- 建置-建立位於程序。
- 成功-建立順利結束。
- 錯誤-建立結束工作流程失敗。
- 取消-建立已取消。
- 取消-建立已取消。

有效建立類型的值︰
- 排名-建立排名。
- 建議的建議建立。


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a model</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T17:51:10Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T17:51:10Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetModelBuildsStatus?modelId='1d20c34f-dca1-4eac-8e5d-f299e4e4ad66'&amp;onlyLastBuild=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###<a name="115-get-builds-status"></a>11.5。 取得建置狀態
擷取建立使用者的所有模型的狀態。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetUserBuildsStatus?onlyLastBuilds=true&apiVersion=%271.0%27`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
|   onlyLastBuild   |   指出是否要傳回的模型的所有建立歷程記錄] 或 [最新建立的狀態。 |
|   apiVersion      |   1.0                                 |


**回應**︰

HTTP 狀態碼︰ 200

回應包含建立每一個項目。 每個項目具有下列資料︰

- `feed/entry/content/properties/UserName`使用者的名稱。
- `feed/entry/content/properties/ModelName`模型的名稱。
- `feed/entry/content/properties/ModelId`-模型的唯一識別碼。
- `feed/entry/content/properties/IsDeployed`-建立部署是否。
- `feed/entry/content/properties/BuildId`-建立唯一識別碼。
- `feed/entry/content/properties/BuildType`-輸入的建立]。
- `feed/entry/content/properties/Status`-建立狀態。 可以是下列其中一項︰ 錯誤、 建置、 佇列、 已取消、 Cancelling、 成功。
- `feed/entry/content/properties/StatusMessage`-（僅適用於特定狀態） 詳細的狀態訊息。
- `feed/entry/content/properties/Progress`-建立進度 （%）。
- `feed/entry/content/properties/StartTime`-建立開始時間。
- `feed/entry/content/properties/EndTime`-建立結束時間。
- `feed/entry/content/properties/ExecutionTime`-建立工期。
- `feed/entry/content/properties/ProgressStep`建立進行中的目前階段的相關詳細資料。

有效建立狀態︰
- 建立-建立建立要求輸入。
- 佇列-觸發建立要求，以及它佇列。
- 建置-建立位於程序。
- 成功-建立順利結束。
- 錯誤-建立結束工作流程失敗。
- 取消-建立已取消。
- 取消-建立已取消。


有效建立類型的值︰
- 排名-建立排名。
- 建議的建議建立。


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get builds status of a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-05T18:41:21Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildsStatusEntity</title>
            <updated>2014-11-05T18:41:21Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserBuildsStatus?onlyLastBuilds=False&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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


###<a name="116-delete-build"></a>11.6。 刪除建立
刪除建置。

附註︰ <br>您無法刪除使用中的建立。 應該會以不同的作用中建立更新模型，您將其刪除。<br>您無法刪除進行中建立。 您應致電<strong>取消建立</strong>的第一次取消建立。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/DeleteBuild?buildId=%271500068%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| buildId | 建立的唯一識別碼。 |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200

###<a name="117-cancel-build"></a>11.7。 取消建立
取消建置中建置狀態。

| HTTP 方法 | URI |
|:--------|:--------|
|保留     |`<rootURI>/CancelBuild?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/CancelBuild?buildId=%271500076%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| buildId | 建立的唯一識別碼。 |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200

###<a name="118-get-build-parameters"></a>11.8。 取得建立參數
擷取建立參數。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetBuildParameters?buildId=%27<buildId>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/GetBuildParameters?buildId=%271000653%27&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| buildId | 建立的唯一識別碼。 |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200

此 API 傳回鍵值項目集合。 每個項目代表參數和其值︰
- `feed/entry/content/properties/Key`-建立參數名稱。
- `feed/entry/content/properties/Value`-建立參數值。

下表描述每個機碼表示的值。

|索引鍵|描述|類型|有效的值|
|:-----|:----|:----|:---|
|NumberOfModelIterations | 模型執行重複次數會反映整體計算時間，以及模型精確度。 較高的數字、 較佳的精確度就會出現，但計算時間會採取更長的時間。| 整數 | 10-50 |
| NumberOfModelDimensions | 模型會嘗試尋找您的資料中的 「 功能 」 的數量與維度的數目。 增加維度的數目，可讓更有效地將較小的叢集微調的結果。 不過，太多維度會防止模型尋找項目之間的關聯性。 | 整數 | 10 40 |
|ItemCutOffLowerBound| 定義冷凝器項目下限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|ItemCutOffUpperBound| 定義冷凝器項目上限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|UserCutOffLowerBound| 定義冷凝器使用者下限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
|UserCutOffUpperBound| 定義冷凝器使用者上限。 請參閱上述的使用狀況冷凝器。 | 整數 | 2 或更多 （0 停用冷凝器） |
| 描述 | 建立描述。 | 字串 | 任何文字、 最大 512 個字元 |
| EnableModelingInsights | 可讓您以計算建議模型上的指標。 | 布林值 | True/False |
| UseFeaturesInModel | 指出功能是否可以用來強化建議模型。 | 布林值 | True/False |
| ModelingFeatureList | 逗點分隔功能名稱會在建議建立，用來強化建議的清單。 | 字串 | 功能名稱最多 512 個字元 |
| AllowColdItemPlacement | 指出是否建議也應該推低溫的項目透過功能相似性。 | 布林值 | True/False |
| EnableFeatureCorrelation | 表示是否功能可以使用的原因。 | 布林值 | True/False |
| ReasoningFeatureList | 逗點分隔的用於弄句子 （例如建議說明） 的功能名稱清單。  | 字串 | 功能名稱最多 512 個字元 |


OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get build parameters</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2015-01-08T13:50:57Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'" />
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UseFeaturesInModel</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">AllowColdItemPlacement</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableFeatureCorrelation</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">EnableModelingInsights</d:Key>
                    <d:Value m:type="Edm.String">False</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelIterations</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
            <titletype="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">NumberOfModelDimensions</d:Key>
                    <d:Value m:type="Edm.String">10</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <linkrel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ItemCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffLowerBound</d:Key>
                    <d:Value m:type="Edm.String">2</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">UserCutOffUpperBound</d:Key>
                    <d:Value m:type="Edm.String">2147483647</d:Value>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=10&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ModelingFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=11&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">ReasoningFeatureList</d:Key>
                    <d:Value m:type="Edm.String"/>
                </m:properties>
            </content>
        </entry>
        <entry>
            <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1</id>
            <title type="text">GetBuildParametersEntity</title>
            <updated>2015-01-08T13:50:57Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetBuildParameters?buildId='1000653'&amp;apiVersion='1.0'&amp;$skip=12&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:Key m:type="Edm.String">Description</d:Key>
                    <d:Value m:type="Edm.String">rankBuild</d:Value>
                </m:properties>
            </content>
        </entry>
    </feed>

##<a name="12-recommendation"></a>12。 建議
###<a name="121-get-item-recommendations-for-active-build"></a>12.1。 取得項目建議 （作用中建立）

取得建議類型的作用中建立的 「 建議 」 或者 「 Fbt 」 種子 （輸入） 的項目清單為基礎。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| itemIds | 逗點分隔的建議的項目清單。 <br>如果作用中建立是類型 FBT，您可以傳送只有一個項目。 <br>最大長度︰ 最低 1024年 |
| numberOfResults | 必要的結果數目 <br> 最大值︰ 150 |
| includeMetatadata | 供日後使用，永遠為 false |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200


回應包含一個項目，每個建議的項目。 每個項目具有下列資料︰
- `Feed\entry\content\properties\Id`-建議的項目識別碼。
- `Feed\entry\content\properties\Name`項目的名稱。
- `Feed\entry\content\properties\Rating`-評等的建議。較高的數字表示較高的信賴度。
- `Feed\entry\content\properties\Reasoning`-建議弄 （例如建議說明）。

下列範例回應包含 10 個建議的項目。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=3&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=4&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=5&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=6&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=7&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=8&amp;$top=1" />
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
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1</id>
    <title type="text">GetRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=10&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=9&amp;$top=1" />
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

###<a name="122-get-item-recommendations-of-a-specific-build"></a>12.2。 取得項目 （的特定組建） 的建議

取得特定組建 」 建議 」 或 「 Fbt 」 類型的建議。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/ItemRecommend?modelId=%27<modelId>%27&itemIds=%27<itemId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ItemRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| itemIds | 逗點分隔的建議的項目清單。 <br>如果作用中建立是類型 FBT，您可以傳送只有一個項目。 <br>最大長度︰ 最低 1024年 |
| numberOfResults | 必要的結果數目 <br> 最大值︰ 150  |
| includeMetatadata | 供日後使用，永遠為 false
| buildId | 建立要使用的識別碼此建議要求 |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200


回應包含一個項目，每個建議的項目。 每個項目具有下列資料︰
- `Feed\entry\content\properties\Id`-建議的項目識別碼。
- `Feed\entry\content\properties\Name`項目的名稱。
- `Feed\entry\content\properties\Rating`-評等的建議。較高的數字表示較高的信賴度。
- `Feed\entry\content\properties\Reasoning`-建議弄 （例如建議說明）。

請參閱 12.1 回應範例

###<a name="123-get-fbt-recommendations-for-active-build"></a>12.3。 取得 FBT 建議 （作用中建立）

取得 「 Fbt 」 為基礎的種子 （輸入） 項目類型的作用中建立的建議。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=<double>&includeMetadata=false&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| 項目識別碼 | 建議的項目。 <br>最大長度︰ 最低 1024年 |
| numberOfResults | 必要的結果數目 <br>最大值︰ 150 |
| minimalScore | 應該有常用的設定，才會包含在傳回的結果的最小分數 |
| includeMetatadata | 供日後使用，永遠為 false |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200


回應包含建議的項目設定 （通常種子/輸入項目與購買的項目組） 每一個項目。 每個項目具有下列資料︰
- `Feed\entry\content\properties\Id1`-建議的項目識別碼。
- `Feed\entry\content\properties\Name1`項目的名稱。
- `Feed\entry\content\properties\Id2`（選擇性）-第 2 建議的項目識別碼。
- `Feed\entry\content\properties\Name2`（選擇性） 的第 2 項目名稱。
- `Feed\entry\content\properties\Rating`-評等的建議。較高的數字表示較高的信賴度。
- `Feed\entry\content\properties\Reasoning`-建議弄 （例如建議說明）。

下列範例回應包含 3 的建議的項目集。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
     <subtitle type="text">Get Recommendation</subtitle>
     <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemId='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'" />
    <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">159</d:Id1>
        <d:Name1 m:type="Edm.String">159</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.543343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '159'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=1&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">52</d:Id1>
        <d:Name1 m:type="Edm.String">52</d:Name1>
        <d:Id2 m:type="Edm.String"></d:Id2>
        <d:Name2 m:type="Edm.String"></d:Name2>
        <d:Rating m:type="Edm.Double">0.533343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '52'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
     <entry>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1</id>
    <title type="text">GetFbtRecommendationEntity</title>
    <updated>2014-10-05T12:28:48Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/ItemFbtRecommend?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;itemIds='1003'&amp;numberOfResults=3&amp;minimalScore=0.1&amp;includeMetadata=false&amp;apiVersion='1.0'&amp;$skip=2&amp;$top=1" />
    <content type="application/xml">
      <m:properties>
        <d:Id1 m:type="Edm.String">35</d:Id1>
        <d:Name1 m:type="Edm.String">35</d:Name1>
        <d:Id2 m:type="Edm.String">102</d:Id2>
        <d:Name2 m:type="Edm.String">102</d:Name2>
        <d:Rating m:type="Edm.Double">0.523343480387708</d:Rating>
        <d:Reasoning m:type="Edm.String">People who bought '1003' also bought '35' and '102'</d:Reasoning>
      </m:properties>
    </content>
     </entry>
    </feed>

###<a name="124-get-fbt-recommendations-of-a-specific-build"></a>12.4。 取得 FBT 建議 （的特定組建）

取得特定類型 」 Fbt 」 的建置的建議。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/ItemFbtRecommend?modelId=%27<modelId>%27&itemId=%27<itemId>%27&numberOfResults=<int>&minimalScore=<double>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/ItemFbtRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&itemId=%271003%27&numberOfResults=10&minimalScore=0.1&includeMetadata=false&buildId=1234&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| 項目識別碼 | 建議的項目。 <br>最大長度︰ 最低 1024年 |
| numberOfResults | 必要的結果數目 <br>最大值︰ 150 |
| minimalScore | 應該有常用的設定，才會包含在傳回的結果的最小分數 |
| includeMetatadata | 供日後使用，永遠為 false |
| buildId | 建立要使用的識別碼此建議要求 |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200


回應包含建議的項目設定 （通常種子/輸入項目與購買的項目組） 每一個項目。 每個項目具有下列資料︰
- `Feed\entry\content\properties\Id1`-建議的項目識別碼。
- `Feed\entry\content\properties\Name1`項目的名稱。
- `Feed\entry\content\properties\Id2`（選擇性）-第 2 建議的項目識別碼。
- `Feed\entry\content\properties\Name2`（選擇性） 的第 2 項目名稱。
- `Feed\entry\content\properties\Rating`-評等的建議。較高的數字表示較高的信賴度。
- `Feed\entry\content\properties\Reasoning`-建議弄 （例如建議說明）。

請參閱 12.3 回應範例

###<a name="125-get-user-recommendations-for-active-build"></a>12.5。 取得使用者建議 （作用中建立）

取得使用者建議的 「 建議 」 標示為 [作用中建立的類型的建立。

API 會傳回的預測的項目使用記錄根據使用者的清單。

附註︰ 
 1. 沒有任何使用者為建議 FBT 建立。
 2. 如果是作用中建立 FBT 這個方法會傳回錯誤。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| 使用者識別碼  | 使用者的唯一識別碼 |
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

請參閱 12.1 回應範例

###<a name="126-get-user-recommendations-with-item-list-for-active-build"></a>12.6。 取得使用者建議與項目清單 （作用中建立）

使用者可以推薦組建 」 建議 」 標示為 [作用中建立與其他清單的項目類型

API 會傳回預測的項目使用記錄根據使用者的及其他提供的項目的清單。

附註︰ 
 1. 沒有任何使用者為建議 FBT 建立。
 2. 如果是作用中建立 FBT 這個方法會傳回錯誤。


| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%2C1000%27&numberOfResults=10&includeMetadata=false&apiVersion=%271.0%27`|

|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| 使用者識別碼  | 使用者的唯一識別碼 |
| itemsIds | 逗點分隔的建議的項目清單。 最大長度︰ 最低 1024年 |
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

請參閱 12.1 回應範例

###<a name="127-get-user-recommendations--of-a-specific-build"></a>12.7。 取得使用者 （的特定組建） 的建議

取得特定組建 」 建議 」 類型的使用者建議。

API 會傳回根據使用記錄預測項目 （用於特定建立） 的使用者清單。

附註︰ 沒有任何使用者為建議 FBT 建立。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| 使用者識別碼  | 使用者的唯一識別碼 |
| numberOfResults | 必要的結果數目 |
| includeMetatadata | 供日後使用，永遠為 false |
| buildId | 建立要使用的識別碼此建議要求 |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200


回應包含一個項目，每個建議的項目。 每個項目具有下列資料︰
- `Feed\entry\content\properties\Id`-建議的項目識別碼。
- `Feed\entry\content\properties\Name`項目的名稱。
- `Feed\entry\content\properties\Rating`-評等的建議。較高的數字表示較高的信賴度。
- `Feed\entry\content\properties\Reasoning`-建議弄 （例如建議說明）。

請參閱 12.1 回應範例


###<a name="128-get-user-recommendations-with-item-list-of-a-specific-build"></a>12.8。 取得使用者的建議 （的特定組建） 的項目清單

取得特定組建 」 建議 」 類型的使用者建議及其他項目清單。

API 會傳回預測的項目使用記錄根據使用者的清單及其他項目清單。

注意︰ Tthere 是 FBT 建立沒有使用者建議。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/UserRecommend?modelId=%27<modelId>%27&userId=%27<userId>%27&itemsIds=%27<itemsIds>%27&numberOfResults=<int>&includeMetadata=<bool>&buildId=<int>&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/UserRecommend?modelId=%272779c063-48fb-46c1-bae3-74acddc8c1d1%27&userId=%27u1101%27&itemsIds=%271003%27&numberOfResults=10&includeMetadata=false&buildId=50012&apiVersion=%271.0%27`|



|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| 使用者識別碼  | 使用者的唯一識別碼 |
| itemIds | 逗點分隔的建議的項目清單。 最大長度︰ 最低 1024年 |
| numberOfResults | 必要的結果數目 |
| includeMetatadata | 供日後使用，永遠為 false |
| buildId | 建立要使用的識別碼此建議要求 |
| apiVersion | 1.0 |

**回應︰**

HTTP 狀態碼︰ 200


回應包含一個項目，每個建議的項目。 每個項目具有下列資料︰
- `Feed\entry\content\properties\Id`-建議的項目識別碼。
- `Feed\entry\content\properties\Name`項目的名稱。
- `Feed\entry\content\properties\Rating`-評等的建議。較高的數字表示較高的信賴度。
- `Feed\entry\content\properties\Reasoning`-建議弄 （例如建議說明）。

請參閱 12.1 回應範例

##<a name="13-user-usage-history"></a>13 使用者使用記錄
擷取使用者歷程記錄 （與特定使用者相關聯的項目） 的建議模型已建立後可讓系統用於建立。
此 API 允許擷取使用者歷程記錄

注意︰ 使用者歷程記錄是目前僅適用於建議建置。

###<a name="131-retrieve-user-history"></a>13.1 擷取使用者歷程記錄
擷取是指定的使用者識別碼使用在作用中建立或在指定建立的項目的清單。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     | 取得作用中建立使用者歷程記錄。<br/>`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&apiVersion=%271.0%27`<br/><br/>取得指定建立使用者歷程記錄`<rootURI>/GetUserHistory?modelId=%27<model_id>%27&userId=%27<userId>%27&buildId=<int>&apiVersion=%271.0%27`<br/><br/>範例︰`<rootURI>/GetUserHistory?modelId=%2727967136e8-f868-4258-9331-10d567f87fae%27&&userId=%27u_1013%27&apiVersion=%271.0%277`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼。|
| 使用者識別碼 | 使用者的唯一識別碼。
| buildId | 選用的參數指出哪一個建立的使用者歷程記錄應擷取允許
| apiVersion | 1.0 |


**回應︰**

HTTP 狀態碼︰ 200

回應包含一個項目，每個建議的項目。 每個項目具有下列資料︰
- `Feed\entry\content\properties\Id`-建議的項目識別碼。
- `Feed\entry\content\properties\Name`項目的名稱。
- `Feed\entry\content\properties\Rating`-N/A。
- `Feed\entry\content\properties\Reasoning`-N/A。

OData XML

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
    <title type="text" />
    <subtitle type="text">Get User History</subtitle>
    <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'</id>
    <rights type="text" />
    <updated>2015-05-26T15:32:47Z</updated>
    <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'" />
    <entry>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
        <title type="text">CatalogItemsThatContainATokenEntity</title>
        <updated>2015-05-26T15:32:47Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetUserHistory?modelId='2779c063-48fb-46c1-bae3-74acddc8c1d1'&amp;userId='u_1013'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
        <content type="application/xml">
            <m:properties>
                <d:Id m:type="Edm.String">2406E770-769C-4189-89DE-1C9283F93A96</d:Id>
                <d:Name m:type="Edm.String">Clara Callan</d:Name>
                <d:Rating m:type="Edm.Double">0</d:Rating>
                <d:Reasoning m:type="Edm.String"/>
                <d:Metadata m:type="Edm.String"/>
                <d:FormattedRating m:type="Edm.Double" m:null="true"/>
            </m:properties>
        </content>
    </entry>
</feed>

##<a name="14-notifications"></a>14。 通知
Azure 電腦學習建議建立常設錯誤發生系統中的通知。 有 3 種通知︰
1.  為每個建置失敗觸發建置失敗-此通知。
2.  當有超過 100 個錯誤處理的每個模型使用事件前 5 分鐘觸發資料擷取處理錯誤-此通知。
3.  處理建議要求，每個模型中的最後一個 5 分鐘中有多包含 100 個錯誤，就會觸發建議消耗失敗此通知。


###<a name="141-get-notifications"></a>14.1。 取得通知
擷取所有模型或單一模型的所有通知。

| HTTP 方法 | URI |
|:--------|:--------|
|取得     |`<rootURI>/GetNotifications?modelId=%27<model_id>%27&apiVersion=%271.0%27`<br><br>取得所有模型的所有通知︰<br>`<rootURI>/GetNotifications?apiVersion=%271.0%27`<br><br>取得特定模型通知範例︰<br>`<rootURI>/GetNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%277`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 選擇性參數。 當省略，您會收到所有通知，對於所有的模型。 <br>有效的值︰ 模型的唯一識別碼。|
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應︰**

HTTP 狀態碼︰ 200

OData XML

    The response includes one entry per notification. Each entry has the following data:
        * feed\entry\content\properties\UserName - Internal user name identification.
        * feed\entry\content\properties\ModelId - Model ID.
        * feed\entry\content\properties\Message - Notification message.
        * feed\entry\content\properties\DateCreated - Date that this notification was created in UTC format.
        * feed\entry\content\properties\NotificationType - Notification types. Values are BuildFailure, RecommendationFailure, and DataAquisitionFailure.

    <feed xmlns:base="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications" xmlns:d="http://schemas.microsoft.com/ado/2007/08/dataservices" xmlns:m="http://schemas.microsoft.com/ado/2007/08/dataservices/metadata" xmlns="http://www.w3.org/2005/Atom">
        <title type="text" />
        <subtitle type="text">Get a list of Notifications for a user</subtitle>
        <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'</id>
        <rights type="text" />
        <updated>2014-11-04T13:24:23Z</updated>
        <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'" />
        <entry>
                <id>https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1</id>
            <title type="text">GetAListOfNotificationsForAUserEntity</title>
            <updated>2014-11-04T13:24:23Z</updated>
            <link rel="self" href="https://api.datamarket.azure.com/amla/recommendations/v3/GetNotifications?modelId='967136e8-f868-4258-9331-10d567f87fae'&amp;apiVersion='1.0'&amp;$skip=0&amp;$top=1" />
            <content type="application/xml">
                <m:properties>
                    <d:UserName m:type="Edm.String">515506bc-3693-4dce-a5e2-81cb3e8efb56@dm.com</d:UserName>
                    <d:ModelId m:type="Edm.String">967136e8-f868-4258-9331-10d567f87fae</d:ModelId>
                    <d:Message m:type="Edm.String">Build failed for user</d:Message>
                    <d:DateCreated m:type="Edm.String">2014-11-04T13:23:14.383</d:DateCreated>
                    <d:NotificationType m:type="Edm.String">BuildFailure</d:NotificationType>
                </m:properties>
            </content>
        </entry>
    </feed>

###<a name="142-delete-model-notifications"></a>為 14.2。 刪除模型的通知
刪除所有讀取模型的通知。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteModelNotifications?modelId=%<model_id>%27&apiVersion=%271.0%27`<br><br>範例︰<br>`<rootURI>/DeleteModelNotifications?modelId=%27967136e8-f868-4258-9331-10d567f87fae%27&apiVersion=%271.0%27`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| modelId | 模型的唯一識別碼 |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200

###<a name="143-delete-user-notifications"></a>14.3。 刪除使用者的通知
刪除所有模型的所有通知。

| HTTP 方法 | URI |
|:--------|:--------|
|刪除     |`<rootURI>/DeleteUserNotifications?apiVersion=%271.0%27`|


|   參數名稱  |   有效的值                        |
|:--------          |:--------                              |
| apiVersion | 1.0 |
|||
| 邀請內文 | 無 |

**回應**︰

HTTP 狀態碼︰ 200




##<a name="15-legal"></a>15。 法律
這份文件提供 「 為-是 」。 檢視此文件，包括 URL 及網際網路網站參照，以表示和資訊可能會變更不另行通知。<br><br>
本文所述的部分範例僅供說明提供，虛構。 任何實數關聯或連線或應該推斷。<br><br>
這份文件不提供您任何財產任何 Microsoft 產品中的任何法律權限。 您可以複製並使用這份文件內部，參考之目的。<br><br>
（c) 2015 Microsoft。 保留所有的權限。
 
