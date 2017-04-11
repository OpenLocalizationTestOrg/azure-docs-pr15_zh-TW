<properties 
    pageTitle="如何編碼資產使用 Media Encoder 標準 |Microsoft Azure" 
    description="瞭解如何使用 Media Encoder 標準編碼媒體服務上的媒體內容。 程式碼範例使用 REST API。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="juliako"/>


#<a name="how-to-encode-an-asset-using-media-encoder-standard"></a>如何編碼資產使用標準 Media Encoder


> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
- [其餘](media-services-rest-encode-asset.md)
- [入口網站](media-services-portal-encode.md)

##<a name="overview"></a>概觀
若要透過網際網路傳遞數位影片中，您必須壓縮媒體。 數位視訊檔案很大，而且可能太大無法傳遞透過網際網路或您的客戶裝置正確顯示。 編碼是的讓您的客戶可以檢視您的媒體壓縮視訊和音訊程序。

編碼工作是最常見的處理作業中媒體服務。 您建立的媒體檔案轉換到另一種編碼方式編碼工作。 當您編碼時，您可以使用媒體服務內建編碼器 （媒體 Encoder 標準版）。 您也可以使用所提供的媒體服務夥伴; encoder第三方編碼器都是透過 Azure Marketplace。 您可以指定編碼工作，藉由使用預設為您的 encoder 定義的字串或使用預設的設定檔的詳細資料。 可用的預設格式的類型，請參閱[工作 Media Encoder 標準的預設格式](http://msdn.microsoft.com/library/mt269960)。

每個工作只能有一或多個工作，視您想要完成的處理的類型而定。 透過 REST API，您可以建立作業與他們相關的工作，在兩種方法之一︰

- 工作可以是透過工作實體上的 [工作導覽] 屬性的定義的內嵌或
- 透過 OData 批次處理程序。


建議您隨時都將 MP4 設定，然後再將設定轉換為所要的格式，使用[動態包裝](media-services-dynamic-packaging-overview.md)調整位元率編碼 mezzanine 檔案。 若要利用動態包裝，必須先至少有一個視串流單位取得串流端點從您打算傳送您的內容。 如需詳細資訊，請參閱[如何比例媒體服務](media-services-portal-manage-streaming-endpoints.md)。

如果您的輸出資產加密的儲存空間，您必須設定資產傳遞原則。 如需詳細資訊，請參閱[設定資產傳遞原則](media-services-rest-configure-asset-delivery-policy.md)。


>[AZURE.NOTE]在啟動參照媒體處理器之前，確認您擁有正確的媒體處理器識別碼。 如需詳細資訊，請參閱[取得媒體處理器](media-services-rest-get-media-processor.md)。

##<a name="create-a-job-with-a-single-encoding-task"></a>建立與單一編碼工作的工作

>[AZURE.NOTE] 使用媒體服務 REST API 工作時，下列事項︰
>
>當存取媒體服務中的項目，您必須在 HTTP 邀請中設定特定的標題欄位和值。 如需詳細資訊，請參閱[媒體服務 REST API 開發的設定](media-services-rest-how-to-use.md)。

>成功連接之後要 https://media.windows.net，您會收到指定其他媒體服務 URI 301 重新導向。 您必須進行後續的來電至新的 URI[連線至媒體服務使用 REST API](media-services-rest-connect-programmatically.md)所述。
>
>當使用 JSON 並指定要在邀請中使用**__metadata**關鍵字 （例如，若要參照連結的物件） 您必須設定**接受**標頭[詳細 JSON](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)格式︰ 接受︰ 應用程式/json; odata = 詳細資訊。

下列範例會顯示如何建立和張貼使用任務設定為編碼在特定的解析度和品質視訊的工作。 當編碼方式和媒體 Encoder 標準，您可以使用指定的任務設定預設格式[以下](http://msdn.microsoft.com/library/mt269960)。

要求︰

文章 https://media.windows.net/API/Jobs HTTP/1.1 （英文） 的內容類型︰ 應用程式/json; odata = 詳細接受︰ 應用程式/json; odata = 詳細 DataServiceVersion: 3.0 MaxDataServiceVersion: 3.0 x-ms-版本︰ 2.11 授權︰ 承載者<token value> 
 x-ms-用戶端-要求-識別碼︰ 00000000-0000-0000-0000-000000000000 host （主機): media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Multiple Bitrate 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

回應︰
    
    HTTP/1.1 201 Created

    . . . 

###<a name="set-the-output-assets-name"></a>設定輸出資產的名稱

下列範例會顯示如何設定 assetName 屬性︰

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

##<a name="considerations"></a>考量

- TaskBody 屬性必須使用常值的 XML 定義的輸入或輸出會使用該任務的資產。 任務主題包含 XML XML 結構描述定義。
- 在 TaskBody 定義中，每個內部值的<inputAsset>和<outputAsset>必須設定為 JobInputAsset(value) 或 JobOutputAsset(value)。
- 工作可以有多個輸出資產。 為工作中的工作成果只使用一次一個 JobOutputAsset(x)。
- 您可以指定 JobInputAsset 或 JobOutputAsset 列為輸入任務的資產。
- 工作必須並未構成循環圖。
- 您傳送至 JobInputAsset 或 JobOutputAsset 值參數代表資產索引值。 實際資產都定義在 InputMediaAssets 和 OutputMediaAssets 導覽上的內容工作實體定義。 
- 透過媒體服務內建在 OData v3，因為參照 InputMediaAssets 和 OutputMediaAssets 導覽屬性集合中的個別資產 」 __metadata: uri 「 名稱 / 值組。
- InputMediaAssets 會對應到媒體服務中建立的一個或多個資產。 系統會建立 OutputMediaAssets。 未參考現有的資產。
- OutputMediaAssets 可以使用 assetName 屬性來命名。 如果此屬性不存在，則 OutputMediaAsset 名稱為任何內部文字的值<outputAsset>項目是含後稱謂的工作名稱] 的值或作業識別碼中的值 （不在已定義名稱屬性大小寫）。 例如，如果您設定 assetName 」 範例 」 的值，然後 OutputMediaAsset Name 屬性則會設定 「 範例 」。 不過，如果您未設定 assetName 的值，但未設定 「 NewJob 」 的工作名稱，然後 OutputMediaAsset 名稱就是 「 JobOutputAsset （值） _NewJob 」。 


##<a name="create-a-job-with-chained-tasks"></a>建立與鏈結工作的工作

在多個應用程式的情況下，開發人員會想要建立一系列處理任務。 在媒體服務，您可以建立一系列鏈結的任務。 每個任務執行不同處理步驟，並可以使用不同的媒體處理器。 鏈結的工作交出資產一到另一個資產上執行的工作線性序列。 不過，要序列中不需要執行中的工作的工作。 當您建立鏈結的工作時，鏈結的**ITask**物件中建立的單一**IJob**物件。

>[AZURE.NOTE] 目前有 30 個工作，每個作業的限制。 如果您需要鍊超過 30 個工作，請建立一個以上的工作，以包含工作。


    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


###<a name="considerations"></a>考量

若要啟用鏈結工作︰

- 必須至少有 2 個工作的工作。
- 必須至少有一個其輸入是輸出的工作中的另一個任務的任務。

## <a name="use-odata-batch-processing"></a>使用 OData 批次處理 

下列範例會示範如何使用 OData 批次處理建立工作及工作。 批次處理資訊，請參閱[開放式資料通訊協定 (OData) 批次處理](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)。
 
    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net
    
    
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}
    
    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary
    
    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    
    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
 


## <a name="create-a-job-using-a-jobtemplate"></a>建立工作使用工作範本


處理多個資產使用一組通用的工作，JobTemplates 就很有用，指定預設工作預設格式]，工作的順序。

下列範例會示範如何使用 TaskTemplate 定義直接建立工作範本。 TaskTemplate 媒體 Encoder 標準為使用 MediaProcessor 編碼資產檔案。不過，也可以用其他 MediaProcessors。 


    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]與其他媒體服務的實體，您必須為每個 TaskTemplate 定義新的 GUID 識別碼，並 taskTemplateId 和識別碼] 屬性中放在您的邀請內文中。 內容識別配置必須遵循識別 Azure 媒體服務實體所述的配置。 此外，JobTemplates 無法進行更新。 不過，您必須建立一個新的更新變更。
 

如果成功，則會傳回下列回應︰
    
    HTTP/1.1 201 Created
    
    . . .


下列範例會示範如何建立參照工作範本識別碼工作︰

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net

    
    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
     

如果成功，則會傳回下列回應︰
    
    HTTP/1.1 201 Created
    
    . . . 



##<a name="media-services-learning-paths"></a>媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="next-steps"></a>後續步驟
現在，您已經知道如何建立編碼 assset 工作，請移至的[方式來查看工作進度與媒體服務](media-services-rest-check-job-progress.md)主題。


##<a name="see-also"></a>另請參閱

[取得媒體處理器](media-services-rest-get-media-processor.md)
