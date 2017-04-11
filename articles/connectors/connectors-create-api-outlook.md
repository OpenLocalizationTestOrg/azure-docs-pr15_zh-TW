<properties
pageTitle="Outlook.com |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 Outlook.com 連接器可讓您管理您的郵件、 行事曆和連絡人。 您可以執行各種動作，例如傳送郵件、 安排會議、 新增連絡人] 等。"
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

# <a name="get-started-with-the-outlookcom-connector"></a>開始使用 Outlook.com 連接器

Outlook.com 連接器可讓您管理您的郵件、 行事曆和連絡人。 您可以執行各種動作，例如傳送郵件、 安排會議、 新增連絡人] 等。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

Outlook.com 連接器可為的動作;有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 Outlook.com 連接線具有下列的動作及/或可用的觸發程序︰

### <a name="outlookcom-actions"></a>Outlook.com 動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|從資料夾擷取電子郵件|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|傳送電子郵件|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|刪除電子郵件的識別碼|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|標示為已讀取電子郵件|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|回覆電子郵件|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|擷取電子郵件附件傳送識別碼|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|傳送電子郵件的多個選項，並等待 [收件者使用其中一個選項來回應|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|傳送 「 核准 」 電子郵件，並等待 [收件者的回應|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|擷取行事曆|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|從 [行事曆擷取項目|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|建立新的事件|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|從 [行事曆會擷取特定項目|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|刪除行事曆項目|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|部分更新的行事曆項目|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|擷取連絡人資料夾|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|從 [連絡人] 資料夾中擷取連絡人|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|建立新連絡人|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|從 [連絡人] 資料夾中擷取特定的連絡人|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|刪除連絡人|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|部分更新連絡人|
### <a name="outlookcom-triggers"></a>Outlook.com 引動程序
您可以接聽這些事件︰

|觸發程序 | 描述|
|--- | ---|
|在 [事件開始|觸發當啟動時即將來臨的行事曆事件的流程|
|在新的電子郵件|新的電子郵件送達時，觸發程序的流程|
|在 [新項目|觸發時建立新的行事曆項目|
|更新項目|觸發修改的行事曆項目時|


## <a name="create-a-connection-to-outlookcom"></a>建立連線到 Outlook.com
若要建立邏輯應用程式 outlook.com，您必須先建立的**連線**，然後提供下列屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供 Outlook.com 認證|
您建立的連線之後，您可以執行的動作並接聽本文所述的觸發程序使用它。

>[AZURE.INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)] 

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。  

## <a name="reference-for-outlookcom"></a>Outlook.com 參考
適用於版本︰ 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
在 [事件開始︰ 觸發當啟動時即將來臨的行事曆事件的流程 

```GET: /Events/OnUpcomingEvents``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|查詢|無|行事曆的唯一識別碼|
|lookAheadTimeInMinutes|整數|沒有|查詢|15|您要繼續進行即將到來的事件時間 （以分鐘為單位）|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|202|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="getemails"></a>GetEmails
取得電子郵件︰ 從資料夾擷取電子郵件 

```GET: /Mail``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderPath|字串|沒有|查詢|收件匣|若要擷取的電子郵件] 資料夾的路徑 (預設: 「 收件匣])|
|頁首|整數|沒有|查詢|10|若要擷取的電子郵件的數字 (預設︰ 10)|
|fetchOnlyUnread|布林值|沒有|查詢|true|擷取未讀取的電子郵件？|
|includeAttachments|布林值|沒有|查詢|false|如果設為 true，附件也會擷取以及電子郵件|
|searchQuery|字串|沒有|查詢|無|若要篩選的電子郵件的搜尋查詢|
|略過|整數|沒有|查詢|0|若要跳過的電子郵件的數字 (預設︰ 0)|
|skipToken|字串|沒有|查詢|無|跳至擷取新頁面的權杖|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="sendemail"></a>SendEmail
傳送電子郵件︰ 傳送電子郵件 

```POST: /Mail``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|emailMessage| |[是]|本文|無|電子郵件|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="deleteemail"></a>DeleteEmail
刪除電子郵件︰ 識別碼刪除電子郵件 

```DELETE: /Mail/{messageId}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|訊息|字串|[是]|路徑|無|若要刪除的電子郵件的識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="markasread"></a>MarkAsRead
標示為已讀取︰ 標示為已讀取電子郵件 

```POST: /Mail/MarkAsRead/{messageId}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|訊息|字串|[是]|路徑|無|讀取電子郵件標示為 id|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="replyto"></a>ReplyTo
回覆電子郵件︰ 以電子郵件的回覆 

```POST: /Mail/ReplyTo/{messageId}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|訊息|字串|[是]|路徑|無|若要回覆電子郵件的識別碼|
|註解|字串|[是]|查詢|無|回覆註解|
|有|布林值|沒有|查詢|false|回覆給所有收件者|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="getattachment"></a>GetAttachment
取得附件︰ 擷取電子郵件附件傳送識別碼 

```GET: /Mail/{messageId}/Attachments/{attachmentId}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|訊息|字串|[是]|路徑|無|電子郵件的識別碼|
|attachmentId|字串|[是]|路徑|無|若要下載附件的識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="onnewemail"></a>OnNewEmail
在新的電子郵件︰ 觸發流程，新的電子郵件送達時 

```GET: /Mail/OnNewEmail``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|folderPath|字串|沒有|查詢|收件匣|若要擷取的 [郵件] 資料夾 (預設︰ 收件匣)|
|若要|字串|沒有|查詢|無|收件者的電子郵件地址|
|從|字串|沒有|查詢|無|從位址|
|重要性|字串|沒有|查詢|標準模式|（[高]、 [標準模式]，[低） 的電子郵件的重要性 (預設︰ 標準模式)|
|fetchOnlyWithAttachment|布林值|沒有|查詢|false|擷取含有附件的電子郵件|
|includeAttachments|布林值|沒有|查詢|false|包含附件|
|subjectFilter|字串|沒有|查詢|無|若要尋找主旨中的字串|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|順利完成作業|
|202|接受|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="sendmailwithoptions"></a>SendMailWithOptions
傳送電子郵件的選項︰ 傳送電子郵件的多個選項，並等待 [收件者使用其中一個選項來回應 

```POST: /mailwithoptions/$subscriptions``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |[是]|本文|無|訂閱要求選項電子郵件|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|201|建立的訂閱|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="sendapprovalmail"></a>SendApprovalMail
「 核准 」 電子郵件傳送︰ 傳送 「 核准 」 電子郵件，並等待 [收件者的回應 

```POST: /approvalmail/$subscriptions``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |[是]|本文|無|訂閱要求核准的電子郵件|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|201|建立的訂閱|
|400|BadRequest|
|401|未獲授權|
|403|禁止|
|500|內部伺服器錯誤|
|預設值|作業失敗。|


## <a name="calendargettables"></a>CalendarGetTables
取得行事曆︰ 擷取行事曆 

```GET: /datasets/calendars/tables``` 

沒有這個通話的參數
#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="calendargetitems"></a>CalendarGetItems
取得事件︰ 擷取從 [行事曆的項目 

```GET: /datasets/calendars/tables/{table}/items``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|要擷取的行事曆的唯一識別碼|
|$filter|字串|沒有|查詢|無|若要限制的項目數 ODATA 篩選查詢|
|$orderby|字串|沒有|查詢|無|針對指定的項目順序 ODATA orderBy 查詢|
|$skip|整數|沒有|查詢|無|若要跳項目數 (預設 = 0)|
|$top|整數|沒有|查詢|無|若要擷取的項目數目上限 (預設 = 256)|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="calendarpostitem"></a>CalendarPostItem
建立事件︰ 建立新的事件 

```POST: /datasets/calendars/tables/{table}/items``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|唯一識別碼的行事曆|
|項目| |[是]|本文|無|若要建立的行事曆項目|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="calendargetitem"></a>CalendarGetItem
取得事件︰ 擷取特定項目從 [行事曆 

```GET: /datasets/calendars/tables/{table}/items/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|唯一識別碼的行事曆|
|識別碼|字串|[是]|路徑|無|若要擷取的行事曆項目的唯一識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="calendardeleteitem"></a>CalendarDeleteItem
刪除事件︰ 刪除行事曆項目 

```DELETE: /datasets/calendars/tables/{table}/items/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|唯一識別碼的行事曆|
|識別碼|字串|[是]|路徑|無|若要刪除的行事曆項目的唯一識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="calendarpatchitem"></a>CalendarPatchItem
更新事件︰ 部分更新的行事曆項目 

```PATCH: /datasets/calendars/tables/{table}/items/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|唯一識別碼的行事曆|
|識別碼|字串|[是]|路徑|無|若要更新的行事曆項目的唯一識別碼|
|項目| |[是]|本文|無|若要更新的行事曆項目|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
在 [新項目︰ 觸發時建立新的行事曆項目 

```GET: /datasets/calendars/tables/{table}/onnewitems``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|唯一識別碼的行事曆|
|$filter|字串|沒有|查詢|無|若要限制的項目數 ODATA 篩選查詢|
|$orderby|字串|沒有|查詢|無|針對指定的項目順序 ODATA orderBy 查詢|
|$skip|整數|沒有|查詢|無|若要跳項目數 (預設 = 0)|
|$top|整數|沒有|查詢|無|若要擷取的項目數目上限 (預設 = 256)|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
在 [更新項目︰ 觸發修改的行事曆項目時 

```GET: /datasets/calendars/tables/{table}/onupdateditems``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|唯一識別碼的行事曆|
|$filter|字串|沒有|查詢|無|若要限制的項目數 ODATA 篩選查詢|
|$orderby|字串|沒有|查詢|無|針對指定的項目順序 ODATA orderBy 查詢|
|$skip|整數|沒有|查詢|無|若要跳項目數 (預設 = 0)|
|$top|整數|沒有|查詢|無|若要擷取的項目數目上限 (預設 = 256)|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="contactgettables"></a>ContactGetTables
取得連絡人資料夾︰ 擷取 [連絡人] 資料夾 

```GET: /datasets/contacts/tables``` 

沒有這個通話的參數
#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="contactgetitems"></a>ContactGetItems
取得連絡人︰ 擷取連絡人從連絡人資料夾 

```GET: /datasets/contacts/tables/{table}/items``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|若要擷取的 [連絡人] 資料夾的唯一識別碼|
|$filter|字串|沒有|查詢|無|若要限制的項目數 ODATA 篩選查詢|
|$orderby|字串|沒有|查詢|無|針對指定的項目順序 ODATA orderBy 查詢|
|$skip|整數|沒有|查詢|無|若要跳項目數 (預設 = 0)|
|$top|整數|沒有|查詢|無|若要擷取的項目數目上限 (預設 = 256)|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="contactpostitem"></a>ContactPostItem
建立連絡人︰ 建立新連絡人 

```POST: /datasets/contacts/tables/{table}/items``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|[連絡人] 資料夾的唯一識別碼|
|項目| |[是]|本文|無|若要建立連絡人|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="contactgetitem"></a>ContactGetItem
取得連絡人︰ 從 [連絡人] 資料夾中擷取特定的連絡人 

```GET: /datasets/contacts/tables/{table}/items/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|[連絡人] 資料夾的唯一識別碼|
|識別碼|字串|[是]|路徑|無|若要聽取連絡人的唯一識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="contactdeleteitem"></a>ContactDeleteItem
刪除連絡人︰ 刪除連絡人 

```DELETE: /datasets/contacts/tables/{table}/items/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|[連絡人] 資料夾的唯一識別碼|
|識別碼|字串|[是]|路徑|無|若要刪除的連絡人的唯一識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="contactpatchitem"></a>ContactPatchItem
更新連絡人︰ 部分更新連絡人 

```PATCH: /datasets/contacts/tables/{table}/items/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|表格|字串|[是]|路徑|無|[連絡人] 資料夾的唯一識別碼|
|識別碼|字串|[是]|路徑|無|若要更新的連絡人的唯一識別碼|
|項目| |[是]|本文|無|若要更新的連絡人項目|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|
|預設值|作業失敗。|


## <a name="object-definitions"></a>物件定義 

### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse [IDictionary [字串，物件]]


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="object"></a>物件


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|



### <a name="sendmessage"></a>SendMessage


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|附件|陣列|無 |
|從|字串|無 |
|副本|字串|無 |
|密件副本|字串|無 |
|主旨|字串|[是] |
|本文|字串|[是] |
|重要性|字串|無 |
|IsHtml|布林值|無 |
|若要|字串|[是] |



### <a name="sendattachment"></a>SendAttachment


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|@odata.type|字串|無 |
|名稱|字串|[是] |
|ContentBytes|字串|[是] |



### <a name="receivemessage"></a>ReceiveMessage


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|無 |
|IsRead|布林值|無 |
|HasAttachment|布林值|無 |
|DateTimeReceived|字串|無 |
|附件|陣列|無 |
|從|字串|無 |
|副本|字串|無 |
|密件副本|字串|無 |
|主旨|字串|[是] |
|本文|字串|[是] |
|重要性|字串|無 |
|IsHtml|布林值|無 |
|若要|字串|[是] |



### <a name="receiveattachment"></a>ReceiveAttachment


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|[是] |
|ContentType|字串|[是] |
|@odata.type|字串|無 |
|名稱|字串|[是] |
|ContentBytes|字串|[是] |



### <a name="digestmessage"></a>DigestMessage


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|主旨|字串|[是] |
|本文|字串|無 |
|重要性|字串|無 |
|摘要|陣列|[是] |
|附件|陣列|無 |
|若要|字串|[是] |



### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse [ReceiveMessage]


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="datasetsmetadata"></a>DataSetsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|表格式|未定義|無 |
|blob|未定義|無 |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|來源|字串|無 |
|顯示名稱|字串|無 |
|urlEncoding|字串|無 |
|tableDisplayName|字串|無 |
|tablePluralName|字串|無 |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|來源|字串|無 |
|顯示名稱|字串|無 |
|urlEncoding|字串|無 |



### <a name="tablemetadata"></a>TableMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|名稱|字串|無 |
|標題|字串|無 |
|x ms-權限|字串|無 |
|x-ms-功能|未定義|無 |
|結構描述|未定義|無 |



### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|sortRestrictions|未定義|無 |
|filterRestrictions|未定義|無 |
|filterFunctions|陣列|無 |



### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|可排序|布林值|無 |
|unsortableProperties|陣列|無 |
|ascendingOnlyProperties|陣列|無 |



### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|可篩選|布林值|無 |
|nonFilterableProperties|陣列|無 |
|requiredProperties|陣列|無 |



### <a name="optionsemailsubscription"></a>OptionsEmailSubscription


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|NotificationUrl|字串|無 |
|訊息|未定義|無 |



### <a name="messagewithoptions"></a>MessageWithOptions


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|主旨|字串|[是] |
|選項|字串|[是] |
|本文|字串|無 |
|重要性|字串|無 |
|附件|陣列|無 |
|若要|字串|[是] |



### <a name="subscriptionresponse"></a>SubscriptionResponse


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|字串|無 |
|資源|字串|無 |
|notificationType|字串|無 |
|notificationUrl|字串|無 |



### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|NotificationUrl|字串|無 |
|訊息|未定義|無 |



### <a name="approvalmessage"></a>ApprovalMessage


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|主旨|字串|[是] |
|選項|字串|[是] |
|本文|字串|無 |
|重要性|字串|無 |
|附件|陣列|無 |
|若要|字串|[是] |



### <a name="approvalemailresponse"></a>ApprovalEmailResponse


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|SelectedOption|字串|無 |



### <a name="tableslist"></a>TablesList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="table"></a>表格


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|名稱|字串|無 |
|顯示名稱|字串|無 |



### <a name="item"></a>項目


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|ItemInternalId|字串|無 |



### <a name="calendaritemslist"></a>CalendarItemsList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="calendaritem"></a>CalendarItem


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|ItemInternalId|字串|無 |



### <a name="contactitemslist"></a>ContactItemsList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="contactitem"></a>ContactItem


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|ItemInternalId|字串|無 |



### <a name="datasetslist"></a>DataSetsList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="dataset"></a>資料集


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|名稱|字串|無 |
|顯示名稱|字串|無 |


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)