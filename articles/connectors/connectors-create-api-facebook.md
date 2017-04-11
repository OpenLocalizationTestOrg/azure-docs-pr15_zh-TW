<properties
    pageTitle="在 [您的邏輯應用程式中新增 Facebook 連接器 |Microsoft Azure"
    description="REST API 參數的 Facebook 連接器的概觀"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-facebook-connector"></a>快速入門 Facebook 連接器
連線至 Facebook，以及張貼的時間表，取得頁面摘要，及其他內容。 

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。


與 Facebook，您可以︰

- 建立您的商務流程根據您收到 Facebook 中的資料。 
- 當您收到新的文章時，請使用引動程序。
- 使用動作的張貼到時間表，取得頁面摘要，及其他內容。 這些動作獲得回應，然後再輸出可用的其他動作。 例如時沒有在時間表上的新的文章，就可以的文章，其推入 Twitter 的摘要。 



若要新增作業邏輯應用程式中，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Facebook 連接器包含下列的觸發程序和動作。 

| 觸發程序 | 動作|
| --- | --- |
| <ul><li>當我時間表上新的文章</li></ul> |<ul><li>取得摘要從我的時間表</li><li>張貼到我的時間表</li><li>當我時間表上新的文章</li><li>取得頁面摘要</li><li>取得使用者時間表</li><li>張貼至頁面</li></ul>

所有的連接器支援 JSON 和 XML 格式的資料。

## <a name="create-a-connection-to-facebook"></a>建立與 Facebook 的連線
當您新增此連接器至您的邏輯應用程式時，您必須授權邏輯應用程式連線至 Facebook。

1. 登入您的 Facebook 帳戶
2. 選取 [**授權**]，並允許您連線，並使用您的 Facebook 的邏輯應用程式。 

>[AZURE.INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此相同的 Facebook 連線。

## <a name="swagger-rest-api-reference"></a>Swagger REST API 參照
適用於版本︰ 1.0。

### <a name="get-feed-from-my-timeline"></a>取得摘要從我的時間表
從登入的使用者時間表取得摘要。  
```GET: /me/feed```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|欄位|字串|沒有|查詢|無 |指定您要傳回的欄位。 範例 （識別碼、 名稱、 圖片）。|
|限制|整數|沒有|查詢| 無|若要擷取的文章數目上限|
|使用|字串|沒有|查詢| 無|限制只有具有附加的位置清單中的文章。|
|篩選|字串|沒有|查詢| 無|擷取符合特定資料流篩選器的文章。|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### <a name="post-to-my-timeline"></a>張貼到我的時間表
張貼至登入的使用者時間表狀態訊息。  
```POST: /me/feed```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|文章|字串 |[是]|本文|無 |張貼新郵件|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### <a name="when-there-is-a-new-post-on-my-timeline"></a>當我時間表上新的文章
登入的使用者時間表上新的文章時，請觸發新流程。  
```GET: /trigger/me/feed```

沒有參數。 

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### <a name="get-page-feed"></a>取得頁面摘要
從指定頁面的摘要中取得文章。  
```GET: /{pageId}/feed```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|分頁識別碼|字串|[是]|路徑| 無|從文章有要擷取的頁面的識別碼。|
|限制|整數|沒有|查詢| 無|若要擷取的文章數目上限|
|include_hidden|布林值|沒有|查詢|無 |是否要包含任何頁面中隱藏的文章|
|欄位|字串|沒有|查詢|無 |指定您要傳回的欄位。 範例 （識別碼、 名稱、 圖片）。|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### <a name="get-user-timeline"></a>取得使用者時間表
取得文章從使用者的時間表。  
```GET: /{userId}/feed```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|使用者識別碼|字串|[是]|路徑|無 |其時間表有要擷取的使用者識別碼。|
|限制|整數|沒有|查詢|無 |若要擷取的文章數目上限|
|使用|字串|沒有|查詢|無 |限制只有具有附加的位置清單中的文章。|
|篩選|字串|沒有|查詢| 無|擷取符合特定資料流篩選器的文章。|
|欄位|字串|沒有|查詢| 無|指定您要傳回的欄位。 範例 （識別碼、 名稱、 圖片）。|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


### <a name="post-to-page"></a>張貼至頁面
張貼至 Facebook 頁面的訊息，以登入的使用者。  
```POST: /{pageId}/feed```

| 名稱|資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|分頁識別碼|字串|[是]|路徑|無 |[張貼] 頁面的識別碼。|
|文章|許多 |[是]|本文|無 |若要張貼的新郵件。|

#### <a name="response"></a>回應
|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義

#### <a name="getfeedresponse"></a>GetFeedResponse

|屬性名稱 | 資料類型 | 所需|
|---|---|---|
|資料|陣列|沒有|

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|資料|陣列|沒有|

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>單一項目設定檔中的摘要 postItem:
使用者、 頁面、 應用程式或群組，可能是設定檔。 

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|
|admin_creator|陣列|沒有|
|標題|字串|沒有|
|created_time|字串|沒有|
|描述|字串|沒有|
|feed_targeting|未定義|沒有|
|從|未定義|沒有|
|圖示|字串|沒有|
|is_hidden|布林值|沒有|
|is_published|布林值|沒有|
|連結|字串|沒有|
|訊息|字串|沒有|
|名稱|字串|沒有|
|object_id|字串|沒有|
|圖片|字串|沒有|
|位置|未定義|沒有|
|隱私權|未定義|沒有|
|屬性|陣列|沒有|
|來源|字串|沒有|
|status_type|字串|沒有|
|本文|字串|沒有|
|針對選取目標|未定義|沒有|
|若要|陣列|沒有|
|類型|字串|沒有|
|updated_time|字串|沒有|
|with_tags|未定義|沒有|

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>單一項目設定檔中的摘要 TriggerItem:
使用者、 頁面、 應用程式或群組，可能是設定檔。

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|
|created_time|字串|沒有|
|從|未定義|沒有|
|訊息|字串|沒有|
|類型|字串|沒有|

#### <a name="adminitem"></a>AdminItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|
|連結|字串|沒有|

#### <a name="propertyitem"></a>PropertyItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|名稱|字串|沒有|
|文字|字串|沒有|
|href|字串|沒有|

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|訊息|字串|[是]|
|連結|字串|沒有|
|圖片|字串|沒有|
|名稱|字串|沒有|
|標題|字串|沒有|
|描述|字串|沒有|
|位置|字串|沒有|
|標記|字串|沒有|
|隱私權|未定義|沒有|
|object_attachment|字串|沒有|

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|訊息|字串|[是]|
|連結|字串|沒有|
|圖片|字串|沒有|
|名稱|字串|沒有|
|標題|字串|沒有|
|描述|字串|沒有|
|動作|陣列|沒有|
|位置|字串|沒有|
|標記|字串|沒有|
|object_attachment|字串|沒有|
|針對選取目標|未定義|沒有|
|feed_targeting|未定義|沒有|
|發佈|布林值|沒有|
|scheduled_publish_time|字串|沒有|
|backdated_time|字串|沒有|
|backdated_time_granularity|字串|沒有|
|child_attachments|陣列|沒有|
|multi_share_end_card|布林值|沒有|

#### <a name="postfeedresponse"></a>PostFeedResponse

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|

#### <a name="profilecollection"></a>ProfileCollection

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|資料|陣列|沒有|

#### <a name="useritem"></a>UserItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|
|first_name|字串|沒有|
|last_name|字串|沒有|
|名稱|字串|沒有|
|[性別|字串|沒有|
|關於|字串|沒有|

#### <a name="actionitem"></a>ActionItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|名稱|字串|沒有|
|連結|字串|沒有|

#### <a name="targetitem"></a>TargetItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|國家/地區|陣列|沒有|
|地區設定|陣列|沒有|
|區域|陣列|沒有|
|城市|陣列|沒有|

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>針對選取目標此文章 FeedTargetItem︰ 物件控制新聞摘要
這些群組中的任何人會看到此文章，其他人有較不可能。 只適用於網頁。

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|國家/地區|陣列|沒有|
|區域|陣列|沒有|
|城市|陣列|沒有|
|age_min|整數|沒有|
|age_max|整數|沒有|
|genders|陣列|沒有|
|relationship_statuses|陣列|沒有|
|interested_in|陣列|沒有|
|college_years|陣列|沒有|
|興趣|陣列|沒有|
|relevant_until|整數|沒有|
|education_statuses|陣列|沒有|
|地區設定|陣列|沒有|

#### <a name="placeitem"></a>PlaceItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|沒有|
|名稱|字串|沒有|
|overall_rating|數字|沒有|
|位置|未定義|沒有|

#### <a name="locationitem"></a>LocationItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|縣/市|字串|沒有|
|國家/地區|字串|沒有|
|緯度|數字|沒有|
|located_in|字串|沒有|
|經緯度|數字|沒有|
|名稱|字串|沒有|
|區域|字串|沒有|
|狀態|字串|沒有|
|街道|字串|沒有|
|郵遞區號|字串|沒有|

#### <a name="privacyitem"></a>PrivacyItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|描述|字串|沒有|
|值|字串|[是]|
|允許|字串|沒有|
|拒絕|字串|沒有|
|朋友|字串|沒有|

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|連結|字串|沒有|
|圖片|字串|沒有|
|image_hash|字串|沒有|
|名稱|字串|沒有|
|描述|字串|沒有|

#### <a name="postphotorequest"></a>PostPhotoRequest

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|url|字串|[是]|
|標題|字串|沒有|

#### <a name="postphotoresponse"></a>PostPhotoResponse

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|[是]|
|post_id|字串|[是]|

#### <a name="postvideorequest"></a>PostVideoRequest

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|videoData|字串|[是]|
|描述|字串|[是]|
|標題|字串|[是]|
|uploadedVideoName|字串|沒有|

#### <a name="getphotoresponse"></a>GetPhotoResponse

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|資料|未定義|[是]|

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|url|字串|[是]|
|is_silhouette|布林值|[是]|
|高度|字串|沒有|
|寬度|字串|沒有|

#### <a name="geteventresponse"></a>GetEventResponse

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|資料|陣列|[是]|

#### <a name="geteventresponseitem"></a>GetEventResponseItem

|屬性名稱 | 資料類型 |所需|
|---|---|---|
|識別碼|字串|[是]|
|名稱|字串|[是]|
|start_time|字串|沒有|
|end_time|字串|沒有|
|時區|字串|沒有|
|位置|字串|沒有|
|描述|字串|沒有|
|ticket_uri|字串|沒有|
|rsvp_status|字串|[是]|


## <a name="next-steps"></a>後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。
