<properties
pageTitle="SendGrid |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 SendGrid 連線提供者，可讓您傳送電子郵件及管理收件者清單。"
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

# <a name="get-started-with-the-sendgrid-connector"></a>快速入門 SendGrid 連接器

SendGrid 連線提供者，可讓您傳送電子郵件及管理收件者清單。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

為的動作; 可 SendGrid 連接器有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 SendGrid 連接器有下列可用的動作。 有任何引動程序。

### <a name="sendgrid-actions"></a>SendGrid 動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|傳送電子郵件使用 SendGrid API （10000 收件者限制）|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|將個別的收件者新增至 [收件者清單|


## <a name="create-a-connection-to-sendgrid"></a>建立 SendGrid 的連線
若建立 SendGrid 邏輯應用程式，您必須先建立的**連線**，然後提供下列屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|ApiKey|[是]|提供您 SendGrid Api 金鑰|
 

>[AZURE.INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

您建立的連線之後，您可以執行的動作並接聽本文所述的觸發程序使用它。

## <a name="reference-for-sendgrid"></a>SendGrid 參考
適用於版本︰ 1.0

## <a name="sendemail"></a>SendEmail
傳送電子郵件︰ 傳送電子郵件使用 SendGrid API （10000 收件者限制） 

```POST: /api/mail.send.json``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|要求| |[是]|本文|無|若要傳送的電子郵件訊息|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|429|太多的邀請|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="addrecipienttolist"></a>AddRecipientToList
將收件者新增至清單︰ 將個別的收件者新增至 [收件者清單 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|listId|字串|[是]|路徑|無|收件者清單的唯一識別碼。|
|recipientId|字串|[是]|路徑|無|收件者的唯一識別碼。|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|400|錯誤的要求|
|401|未獲授權|
|403|禁止|
|404|找不到|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義 

### <a name="emailrequest"></a>EmailRequest


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|從|字串|[是] |
|fromname|字串|無 |
|若要|字串|[是] |
|toname|字串|無 |
|主旨|字串|[是] |
|本文|字串|[是] |
|ishtml|布林值|無 |
|副本|字串|無 |
|ccname|字串|無 |
|密件副本|字串|無 |
|bccname|字串|無 |
|replyto|字串|無 |
|日期|字串|無 |
|頁首|字串|無 |
|檔案|陣列|無 |
|檔案名稱|陣列|無 |



### <a name="emailresponse"></a>EmailResponse


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|訊息|字串|無 |



### <a name="recipientlists"></a>RecipientLists


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|清單|陣列|無 |



### <a name="recipientlist"></a>RecipientList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|名稱|字串|無 |
|recipient_count|整數|無 |



### <a name="recipients"></a>收件者


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|收件者|陣列|無 |



### <a name="recipient"></a>收件者


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|電子郵件|字串|無 |
|last_name|字串|無 |
|first_name|字串|無 |
|識別碼|字串|無 |


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)