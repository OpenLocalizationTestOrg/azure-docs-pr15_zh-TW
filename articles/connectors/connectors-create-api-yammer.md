<properties
pageTitle="邏輯應用程式中新增 Yammer 連接器 |Microsoft Azure"
description="Yammer REST API 參數的連接器的概觀"
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

# <a name="get-started-with-the-yammer-connector"></a>開始使用 Yammer 連接器

連線至 Yammer 至企業網路中的 access 交談。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。

使用 Yammer，您可以︰

- 建立您的商務流程根據您收到來自 Yammer 的資料。 
- 有新郵件中群組或摘要您下列時的觸發程序使用。
- 若要張貼訊息，請取得所有郵件，以及其他使用動作。 這些動作獲得回應，然後再輸出可用的其他動作。 例如，新的訊息出現時，您可以傳送使用 Office 365 電子郵件。

若要新增作業邏輯應用程式中，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作
Yammer 包含下列的引動程序和動作。 

觸發程序 | 動作
--- | ---
<ul><li>在群組中的新郵件時</li><li>我的下列新的郵件，則摘要</li></ul>| <ul><li>取得所有訊息</li><li>取得群組中的郵件</li><li>取得從我的下列訊息摘要</li><li>張貼訊息</li><li>在群組中的新郵件時</li><li>我的下列新的郵件，則摘要</li></ul>

所有的連接器支援 JSON 和 XML 格式的資料。 

## <a name="create-a-connection-to-yammer"></a>建立與 Yammer 的連線
若要使用 Yammer 連接器，您第一次建立的**連線**，然後提供這些屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供的 Yammer 認證|

>[AZURE.INCLUDE [Steps to create a connection to Yammer](../../includes/connectors-create-api-yammer.md)]


>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="yammer-rest-api-reference"></a>Yammer REST API 參照
本文適用於版本︰ 1.0


### <a name="get-all-public-messages-in-the-logged-in-users-yammer-network"></a>登入使用者的 Yammer 網路中取得所有公用的訊息
對應至 「 所有 」 的交談在 Yammer 網路介面。  
```GET: /messages.json```

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|older_then|整數|沒有|查詢|無|傳回郵件早於指定為數字字串的訊息識別碼。 這是很適合用於分頁郵件。 例如，如果您目前正在檢視 20 郵件和最舊是一個數字 2912，您可以新增 「？ older_than = 2912″ 取得 20 郵件之前所看到的您的要求。|
|newer_then|整數|沒有|查詢|無|傳回的數字字串所指定的郵件識別碼比訊息。 這應該投票，以新郵件時使用。 如果您看到的訊息，並為 3516 傳回最新的訊息，您可以進行的要求的參數 」？ newer_than = 3516″，以確保的未取得郵件的複本已在頁面上。|
|限制|整數|沒有|查詢|無|傳回只有指定的郵件數。|
|頁面|整數|沒有|查詢|無|取得所指定的頁面。 如果傳回資料大於限制，您可以使用此欄位來存取後續的頁面|


### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|408|要求逾時|
|429|太多要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|503|Yammer 服務無法使用|
|504|閘道器會出現逾時|
|預設值|作業失敗。|


### <a name="post-a-message-to-a-group-or-all-company-feed"></a>張貼訊息給群組或全公司摘要
如果提供群組識別碼，則郵件會張貼到指定的群組還它會張貼在公司的所有摘要。    
```POST: /messages.json``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|輸入| |[是]|本文|無|張貼訊息邀請|


### <a name="response"></a>回應

|名稱|描述|
|---|---|
|201|建立|



## <a name="object-definitions"></a>物件定義

#### <a name="message-yammer-message"></a>訊息︰ Yammer 訊息

| 名稱 | 資料類型 | 所需 |
|---|---| --- | 
|識別碼|整數|沒有|
|content_excerpt|字串|沒有|
|sender_id|整數|沒有|
|replied_to_id|整數|沒有|
|created_at|字串|沒有|
|network_id|整數|沒有|
|message_type|字串|沒有|
|sender_type|字串|沒有|
|url|字串|沒有|
|web_url|字串|沒有|
|group_id|整數|沒有|
|本文|未定義|沒有|
|thread_id|整數|沒有|
|direct_message|布林值|沒有|
|client_type|字串|沒有|
|client_url|字串|沒有|
|語言|字串|沒有|
|notified_user_ids|陣列|沒有|
|隱私權|字串|沒有|
|liked_by|未定義|沒有|
|system_message|布林值|沒有|

#### <a name="postoperationrequest-represents-a-post-request-for-yammer-connector-to-post-to-yammer"></a>PostOperationRequest︰ 表示的文章要求 Yammer 連接器張貼至 yammer

| 名稱 | 資料類型 | 所需 |
|---|---| --- | 
|本文|字串|[是]|
|group_id|整數|沒有|
|replied_to_id|整數|沒有|
|direct_to_id|整數|沒有|
|廣播|布林值|沒有|
|topic1|字串|沒有|
|topic2|字串|沒有|
|topic3|字串|沒有|
|topic4|字串|沒有|
|topic5|字串|沒有|
|topic6|字串|沒有|
|topic7|字串|沒有|
|topic8|字串|沒有|
|topic9|字串|沒有|
|topic10|字串|沒有|
|topic11|字串|沒有|
|topic12|字串|沒有|
|topic13|字串|沒有|
|topic14|字串|沒有|
|topic15|字串|沒有|
|topic16|字串|沒有|
|topic17|字串|沒有|
|topic18|字串|沒有|
|topic19|字串|沒有|
|topic20|字串|沒有|

#### <a name="messagelist-list-of-messages"></a>郵件清單 MessageList:

| 名稱 | 資料類型 | 所需 |
|---|---| --- | 
|郵件|陣列|沒有|


#### <a name="messagebody-message-body"></a>MessageBody︰ 郵件本文

| 名稱 | 資料類型 | 所需 |
|---|---| --- | 
|剖析|字串|沒有|
|純|字串|沒有|
|rtf|字串|沒有|

#### <a name="likedby-liked-by"></a>LikedBy︰ 按讚，

| 名稱 | 資料類型 | 所需 |
|---|---| --- | 
|字數統計|整數|沒有|
|名稱|陣列|沒有|

#### <a name="yammmerentity-liked-by"></a>YammmerEntity︰ 按讚，

| 名稱 | 資料類型 | 所需 |
|---|---| --- | 
|類型|字串|沒有|
|識別碼|整數|沒有|
|full_name|字串|沒有|


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

[1]: ./media/connectors-create-api-yammer/connectionconfig1.png
[2]: ./media/connectors-create-api-yammer/connectionconfig2.png 
[3]: ./media/connectors-create-api-yammer/connectionconfig3.png
[4]: ./media/connectors-create-api-yammer/connectionconfig4.png
[5]: ./media/connectors-create-api-yammer/connectionconfig5.png
