<properties
pageTitle="RSS |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 RSS 連接器可讓使用者發佈並擷取摘要項目。 也可讓使用者的新項目發佈至摘要時觸發作業。"
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-rss-connector"></a>開始使用 RSS 連接器
RSS 是用來發佈經常更新的內容 – 部落格和新聞標題等熱門網站新聞訂閱方式格式。  多個內容發行者提供若要允許使用者訂閱 RSS 摘要。  使用 RSS 連接器 RSS 摘要中發佈新的項目時，擷取摘要的觸發程序和資訊流程。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

RSS 連接器可為的動作;有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 RSS 連接線具有下列的動作及/或可用的觸發程序︰

### <a name="rss-actions"></a>RSS 動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|取得所有的 RSS 摘要的項目。|
### <a name="rss-triggers"></a>RSS 引動程序
您可以接聽這些事件︰

|觸發程序 | 描述|
|--- | ---|
|何時發佈新摘要項目|發佈新摘要時觸發工作流程|


## <a name="create-a-connection-to-rss"></a>建立 RSS 的連線

>[AZURE.INCLUDE [Steps to create a connection to an RSS feed](../../includes/connectors-create-api-rss.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="reference-for-rss"></a>RSS 參考
適用於版本︰ 1.0

## <a name="onnewfeed"></a>OnNewFeed
當新的摘要項目發佈︰ 觸發工作流程發佈新摘要時 

```GET: /OnNewFeed``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|feedUrl|字串|[是]|查詢|無|摘要 url|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|202|接受|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="listfeeditems"></a>ListFeedItems
列出所有的 RSS 摘要: 取得所有的 RSS 摘要的項目。 項。 

```GET: /ListFeedItems``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|feedUrl|字串|[是]|查詢|無|摘要 url|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|202|接受|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義 

### <a name="triggerbatchresponsefeeditem"></a>TriggerBatchResponse [FeedItem]


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="feeditem"></a>FeedItem


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|[是] |
|標題|字串|[是] |
|內容|字串|[是] |
|連結|陣列|無 |
|updatedOn|字串|無 |


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)