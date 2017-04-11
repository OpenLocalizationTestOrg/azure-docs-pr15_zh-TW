<properties
pageTitle=" 使用邏輯應用程式中的寬限時間的連接器 |Microsoft Azure"
description="開始使用您的 Microsoft Azure 應用程式服務邏輯應用程式中的可寬延時間連接器"
services=""    
documentationCenter=""     
authors="msftman"    
manager="erikre"    
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="05/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-slack-connector"></a>開始使用可寬延時間的連接器

可寬延時間是小組通訊工具，結合，所有您的小組通訊中放置，請立即搜尋和使用您的地方。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。

可寬延時間的連接線，您可以︰

* 用來建立邏輯相關應用程式

若要新增作業邏輯應用程式中，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="lets-talk-about-triggers-and-actions"></a>淺談引動程序及動作

可寬延時間的連接器可為的動作;有任何引動程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 可寬延時間的連接器具有下列的動作及/或可用的觸發程序︰

### <a name="slack-actions"></a>可寬延時間的動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|PostMessage|張貼到指定的頻道的訊息。|
## <a name="create-a-connection-to-slack"></a>建立可寬延時間的連線
若要使用的寬限時間的連接器，您第一次建立的**連線**，然後提供這些屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供可寬延時間的認證|

請遵循這些步驟來登入可寬延時間，完成寬限時間**連線**邏輯應用程式中的設定︰

1. 選取 [**循環**
2. 選取的**頻率**，並輸入**間隔**
3. 選取 [**新增動作**  
![設定可寬延時間][1]  
4. 在 [搜尋] 方塊中輸入可寬延時間，然後搜尋名稱中傳回可寬延時間的所有項目，請等候
5. 選取 [**可寬延時間-張貼訊息**
6. 選取 [**登入可寬延時間**︰  
![設定可寬延時間][2]
7. 提供可寬延時間授權應用程式登入認證    
![設定可寬延時間][3]  
8. 您會被重新導向頁面中的貴組織的記錄檔。 **授權**可寬延時間互動邏輯應用程式︰      
![設定可寬延時間][5] 
9. 授權完成後會被重新導向至您完成設定 [**可寬延時間-取得所有郵件**] 區段中的邏輯應用程式。 新增其他引動程序和您所需要的動作。  
![設定可寬延時間][6]
10. 上述功能表列上選取 [**儲存**]，儲存您的工作。


>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="slack-rest-api-reference"></a>可寬延時間 REST API 參照
#### <a name="this-documentation-is-for-version-10"></a>本文適用於版本︰ 1.0


### <a name="post-a-message-to-a-specified-channel"></a>張貼到指定頻道的訊息。
**```POST: /chat.postMessage```** 



| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|頻道|字串|[是]|查詢|無|頻道、 私人群組或 IM 頻道来傳送訊息。 可以是名稱 (例如︰ #general) 或編碼的識別碼。|
|文字|字串|[是]|查詢|無|若要傳送訊息的文字。 格式設定選項，請參閱 https://api.slack.com/docs/formatting。|
|使用者名稱|字串|沒有|查詢|無|傀儡的名稱|
|as_user|布林值|沒有|查詢|無|傳遞張貼訊息以經驗證的使用者，而不是，為傀儡，則為 true|
|剖析|字串|沒有|查詢|無|變更郵件的處理方式。 如需詳細資訊，請參閱 https://api.slack.com/docs/formatting。|
|link_names|整數|沒有|查詢|無|尋找及連結的頻道名稱和使用者名稱。|
|unfurl_links|布林值|沒有|查詢|無|傳遞將 true 變為啟用 unfurling 的主要文字為基礎的內容。|
|unfurl_media|布林值|沒有|查詢|無|傳遞停用的媒體內容 unfurling false。|
|icon_url|字串|沒有|查詢|無|以作為這則訊息圖示圖像 URL|
|icon_emoji|字串|沒有|查詢|無|Emoji 作為這則訊息圖示|


### <a name="here-are-the-possible-responses"></a>以下是可能的回應︰

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|408|要求逾時|
|429|太多要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|503|可寬延時間服務無法使用|
|504|閘道器會出現逾時|
|預設值|作業失敗。|
------



## <a name="object-definitions"></a>物件定義︰ 

 **訊息**︰ Yammer 郵件

郵件的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|識別碼|整數|
|content_excerpt|字串|
|sender_id|整數|
|replied_to_id|整數|
|created_at|字串|
|network_id|整數|
|message_type|字串|
|sender_type|字串|
|url|字串|
|web_url|字串|
|group_id|整數|
|本文|未定義|
|thread_id|整數|
|direct_message|布林值|
|client_type|字串|
|client_url|字串|
|語言|字串|
|notified_user_ids|陣列|
|隱私權|字串|
|liked_by|未定義|
|system_message|布林值|



 **PostOperationRequest**︰ 代表文章要求 Yammer 連接器張貼至 yammer

PostOperationRequest 的必要的屬性︰

本文

**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|本文|字串|
|group_id|整數|
|replied_to_id|整數|
|direct_to_id|整數|
|廣播|布林值|
|topic1|字串|
|topic2|字串|
|topic3|字串|
|topic4|字串|
|topic5|字串|
|topic6|字串|
|topic7|字串|
|topic8|字串|
|topic9|字串|
|topic10|字串|
|topic11|字串|
|topic12|字串|
|topic13|字串|
|topic14|字串|
|topic15|字串|
|topic16|字串|
|topic17|字串|
|topic18|字串|
|topic19|字串|
|topic20|字串|



 **MessageList**︰ 郵件清單

MessageList 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|郵件|陣列|



 **MessageBody**︰ 郵件本文

MessageBody 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|剖析|字串|
|純|字串|
|rtf|字串|



 **LikedBy**︰ 以按讚

LikedBy 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|字數統計|整數|
|名稱|陣列|



 **YammmerEntity**︰ 以按讚

YammmerEntity 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|類型|字串|
|識別碼|整數|
|full_name|字串|


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)
## <a name="object-definitions"></a>物件定義︰ 

 **WebResultModel**: Bing web 搜尋結果

WebResultModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|標題|字串|
|描述|字串|
|DisplayUrl|字串|
|識別碼|字串|
|FullUrl|字串|



 **VideoResultModel**: Bing 視訊的搜尋結果

VideoResultModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|標題|字串|
|DisplayUrl|字串|
|識別碼|字串|
|MediaUrl|字串|
|執行階段|整數|
|縮圖|未定義|



 **ThumbnailModel**︰ 縮圖屬性的多媒體項目

ThumbnailModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|MediaUrl|字串|
|ContentType|字串|
|寬度|整數|
|高度|整數|
|檔案大小|整數|



 **ImageResultModel**: Bing 圖像搜尋結果

ImageResultModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|標題|字串|
|DisplayUrl|字串|
|識別碼|字串|
|MediaUrl|字串|
|SourceUrl|字串|
|縮圖|未定義|



 **NewsResultModel**: Bing 新聞搜尋結果

NewsResultModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|標題|字串|
|描述|字串|
|DisplayUrl|字串|
|識別碼|字串|
|來源|字串|
|日期|字串|



 **SpellResultModel**: Bing 拼字建議結果

SpellResultModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|識別碼|字串|
|值|字串|



 **RelatedSearchResultModel**: Bing 相關的搜尋結果

RelatedSearchResultModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|標題|字串|
|識別碼|字串|
|BingUrl|字串|



 **CompositeSearchResultModel**: Bing 複合索引的搜尋結果

CompositeSearchResultModel 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|WebResultsTotal|整數|
|ImageResultsTotal|整數|
|VideoResultsTotal|整數|
|NewsResultsTotal|整數|
|SpellSuggestionsTotal|整數|
|WebResults|陣列|
|ImageResults|陣列|
|VideoResults|陣列|
|NewsResults|陣列|
|SpellSuggestionResults|陣列|
|RelatedSearchResults|陣列|


## <a name="object-definitions"></a>物件定義︰ 

 **PostOperationResponse**︰ 張貼至可寬延時間表示可寬延時間連接器的後置作業的回應

PostOperationResponse 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|確定|布林值|
|頻道|字串|
|ts|字串|
|訊息|未定義|
|錯誤|字串|



 **MessageItem**︰ 頻道郵件。

MessageItem 的必要的屬性︰


沒有必要屬性。 


**所有的屬性**︰ 


| 名稱 | 資料類型 |
|---|---|
|文字|字串|
|識別碼|字串|
|使用者|字串|
|建立|整數|
|is_user 刪除|布林值|


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png
