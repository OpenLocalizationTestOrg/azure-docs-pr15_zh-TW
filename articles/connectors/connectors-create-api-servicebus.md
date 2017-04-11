<properties
pageTitle="瞭解如何邏輯應用程式中使用 Azure 服務匯流排連接器 |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 連線至 Azure 服務匯流排傳送及接收郵件。 您可以執行動作，例如傳送至佇列、 主題、 接收來自佇列中，從傳送和接收訂閱。"
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
ms.date="08/02/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-azure-service-bus-connector"></a>開始使用 Azure 服務匯流排連接器

連線至 Azure 服務匯流排傳送及接收郵件。 您可以執行動作，例如傳送至佇列、 主題、 接收來自佇列中，從傳送和接收訂閱。

若要使用[任一個連接器](./apis-list.md)，您必須建立邏輯應用程式。 您可以開始建立[現在邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="connect-to-service-bus"></a>連線至服務匯流排

邏輯應用程式可以存取任何服務之前，您必須建立該服務的連線。 一個[連線](./connectors-overview.md)提供邏輯應用程式與其他服務之間的連線。  

>[AZURE.INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]

## <a name="use-a-service-bus-trigger"></a>使用服務匯流排觸發程序

觸發程序是可以用來開始工作流程中的邏輯應用程式定義的事件。 [深入瞭解引動程序](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。  

>[AZURE.INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]  

## <a name="use-a-service-bus-action"></a>使用服務匯流排指令

動作是在邏輯應用程式中定義工作流程所執行的作業。 [深入瞭解動作](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts)。

[AZURE.INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]  

## <a name="technical-details"></a>技術詳細資料

以下是引動程序、 動作和回應此連線支援的相關詳細資料。

### <a name="service-bus-triggers"></a>服務匯流排引動程序

服務匯流排具有下列引動程序︰  

|觸發程序 | 描述|
|--- | ---|
|[當佇列中收到的郵件](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|佇列中收到一則訊息時，這項作業觸發程序的流程。|
|[在主題訂閱時收到的郵件](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|主題訂閱中收到一則訊息時，這項作業觸發程序的流程。|


### <a name="service-bus-actions"></a>服務匯流排動作

服務匯流排具有下列動作︰


|巨集指令|描述|
|--- | ---|
|[傳送郵件](connectors-create-api-servicebus.md#send-message)|這項作業佇列中或主題傳送訊息。|
### <a name="action-and-trigger-details"></a>巨集指令和觸發程序的詳細資料

以下是動作的詳細資料及此連接器，以及其回應的觸發程序。



#### <a name="send-message"></a>傳送郵件

|屬性名稱| 顯示名稱|描述|
| ---|---|---|
|ContentData *|內容|郵件的內容。|
|ContentType|內容類型|郵件內容的內容類型。|
|屬性|屬性|每個關鍵值組代理屬性。|
|entityName *|佇列/主題名稱|佇列中或主題的名稱。|

這些進階參數，也會出現︰

|屬性名稱| 顯示名稱|描述|
| ---|---|---|
|訊息|郵件識別碼|使用者定義的值，以識別重複的訊息，可以使用服務匯流排，如果啟用。|
|若要|若要|若要傳送的地址。|
|ReplyTo|若要回覆|若要回覆佇列中的地址。|
|ReplyToSessionId|回覆工作階段識別碼|若要回覆工作階段的識別碼。|
|標籤|標籤|特定應用程式的標籤。|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|日期及時間，以 utc 表示，當郵件便會新增至佇列中。|
|工作階段識別碼|工作階段識別碼|工作階段的識別碼。|
|相互關聯識別碼|相互關聯識別碼|相互關聯識別碼。|
|TimeToLive|存留時間|在 [刻度]，郵件是有效期間。 從時的訊息會傳送至 [服務匯流排開始工期。|



* 表示必要屬性。


#### <a name="when-a-message-is-received-in-a-queue"></a>當佇列中收到的郵件

|屬性名稱| 顯示名稱|描述|
| ---|---|---|
|queueName *|佇列名稱|佇列中的名稱。|


* 表示必要屬性。


##### <a name="output-details"></a>輸出詳細資料

ServiceBusMessage︰ 這個物件都有內容和服務匯流排郵件的內容。


| 屬性名稱 | 資料類型 | 描述 |
|---|---|---|
|ContentData|字串|郵件的內容。|
|ContentType|字串|郵件內容的內容類型。|
|屬性|物件|每個關鍵值組代理屬性。|
|訊息|字串|使用者定義的值，以識別重複的訊息，可以使用服務匯流排，如果啟用。|
|若要|字串|傳送至地址。|
|ReplyTo|字串|若要回覆佇列中的地址。|
|ReplyToSessionId|字串|若要回覆工作階段的識別碼。|
|標籤|字串|特定應用程式的標籤。|
|ScheduledEnqueueTimeUtc|字串|日期及時間，以 utc 表示，當郵件便會新增至佇列中。|
|工作階段識別碼|字串|工作階段的識別碼。|
|相互關聯識別碼|字串|相互關聯識別碼。|
|TimeToLive|字串|在 [刻度]，郵件是有效期間。 從時的訊息會傳送至 [服務匯流排開始工期。|




#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>在主題訂閱時收到的郵件

|屬性名稱| 顯示名稱|描述|
| ---|---|---|
|topicName *|主題名稱|主題的名稱。|
|subscriptionName *|主題訂閱名稱|主題訂閱的名稱。|


* 表示必要屬性。


##### <a name="output-details"></a>輸出詳細資料

ServiceBusMessage︰ 這個物件都有內容和服務匯流排郵件的內容。


| 屬性名稱 | 資料類型 | 描述 |
|---|---|---|
|ContentData|字串|郵件的內容。|
|ContentType|字串|郵件內容的內容類型。|
|屬性|物件|每個關鍵值組代理屬性。|
|訊息|字串|使用者定義的值，以識別重複的訊息，可以使用服務匯流排，如果啟用。|
|若要|字串|傳送至地址。|
|ReplyTo|字串|若要回覆佇列中的地址。|
|ReplyToSessionId|字串|若要回覆工作階段的識別碼。|
|標籤|字串|特定應用程式的標籤。|
|ScheduledEnqueueTimeUtc|字串|日期及時間，以 utc 表示，當郵件便會新增至佇列中。|
|工作階段識別碼|字串|工作階段的識別碼。|
|相互關聯識別碼|字串|相互關聯識別碼。|
|TimeToLive|字串|在 [刻度]，郵件是有效期間。 從時的訊息會傳送至 [服務匯流排開始工期。|



### <a name="http-responses"></a>HTTP 回應

上述的 [動作]，[觸發程序可以傳回一或多個下列 HTTP 狀態碼︰

|名稱|描述|
|---|---|
|200|[確定]|
|202|接受|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 未知錯誤。|
|預設值|作業失敗。|

## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。
