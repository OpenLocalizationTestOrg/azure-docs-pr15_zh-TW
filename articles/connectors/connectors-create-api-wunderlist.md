<properties
pageTitle="Wunderlist |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 Wunderlist 提供 todo 清單與工作管理員，以協助他人取得完成其內容。  無論您使用愛共用購物清單，處理專案，或規劃假期 Wunderlist 輕鬆擷取、 共用及完成您 to¬dos。 您就可以從任何位置存取您的工作，Wunderlist 立即同步處理您的電話、 平板電腦和電腦之間。"
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

# <a name="get-started-with-the-wunderlist-connector"></a>快速入門 Wunderlist 連接器

Wunderlist 提供 todo 清單與工作管理員，以協助他人取得完成其內容。  無論您使用愛共用購物清單，處理專案，或規劃假期 Wunderlist 輕鬆擷取、 共用及完成您 to¬dos。 您就可以從任何位置存取您的工作，Wunderlist 立即同步處理您的電話、 平板電腦和電腦之間。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

為的動作; 可 Wunderlist 連接器有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 Wunderlist 連接線具有下列的動作及/或可用的觸發程序︰

### <a name="wunderlist-actions"></a>Wunderlist 動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[RetrieveLists](connectors-create-api-wunderlist.md#retrievelists)|擷取您的帳戶相關聯的清單。|
|[CreateList](connectors-create-api-wunderlist.md#createlist)|建立清單。|
|[ListTasks](connectors-create-api-wunderlist.md#listtasks)|擷取任務特定清單。|
|[CreateTask](connectors-create-api-wunderlist.md#createtask)|建立工作|
|[ListSubTasks](connectors-create-api-wunderlist.md#listsubtasks)|從特定清單或特定的任務，請擷取子任務。|
|[CreateSubTask](connectors-create-api-wunderlist.md#createsubtask)|建立子任務在特定的任務|
|[ListNotes](connectors-create-api-wunderlist.md#listnotes)|擷取特定清單或特定工作的筆記。|
|[CreateNote](connectors-create-api-wunderlist.md#createnote)|將記事新增至特定的任務|
|[ListComments](connectors-create-api-wunderlist.md#listcomments)|擷取任務特定清單或特定工作的註解。|
|[CreateComment](connectors-create-api-wunderlist.md#createcomment)|將註解新增至特定的任務|
|[RetrieveReminders](connectors-create-api-wunderlist.md#retrievereminders)|擷取特定清單或特定工作的提醒。|
|[CreateReminder](connectors-create-api-wunderlist.md#createreminder)|設定提醒。|
|[RetrieveFiles](connectors-create-api-wunderlist.md#retrievefiles)|擷取特定清單或特定工作的檔案。|
|[GetList](connectors-create-api-wunderlist.md#getlist)|擷取特定清單|
|[DeleteList](connectors-create-api-wunderlist.md#deletelist)|刪除清單|
|[UpdateList](connectors-create-api-wunderlist.md#updatelist)|更新特定清單|
|[GetTask](connectors-create-api-wunderlist.md#gettask)|擷取特定的任務|
|[UpdateTask](connectors-create-api-wunderlist.md#updatetask)|更新特定的任務|
|[DeleteTask](connectors-create-api-wunderlist.md#deletetask)|刪除特定的任務|
|[GetSubTask](connectors-create-api-wunderlist.md#getsubtask)|擷取特定的子任務|
|[UpdateSubTask](connectors-create-api-wunderlist.md#updatesubtask)|更新特定的子任務|
|[DeleteSubTask](connectors-create-api-wunderlist.md#deletesubtask)|刪除特定的子任務|
|[GetNote](connectors-create-api-wunderlist.md#getnote)|擷取特定的記事|
|[UpdateNote](connectors-create-api-wunderlist.md#updatenote)|更新特定的記事|
|[DeleteNote](connectors-create-api-wunderlist.md#deletenote)|刪除特定的記事|
|[GetComment](connectors-create-api-wunderlist.md#getcomment)|擷取特定任務註解|
|[UpdateReminder](connectors-create-api-wunderlist.md#updatereminder)|更新特定的提醒|
|[DeleteReminder](connectors-create-api-wunderlist.md#deletereminder)|刪除特定的提醒|
### <a name="wunderlist-triggers"></a>Wunderlist 引動程序
您可以接聽這些事件︰

|觸發程序 | 描述|
|--- | ---|
|工作到期時|在清單中的工作到期時，將新流程觸發程序|
|建立新任務的時機|在清單中建立新的任務時，將新的流程觸發程序|
|提醒的時機|提醒發生時，將新的流程觸發程序|


## <a name="create-a-connection-to-wunderlist"></a>建立 Wunderlist 的連線
若要建立 Wunderlist 邏輯應用程式，您必須先建立的**連線**，然後提供下列屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供 Wunderlist 認證|
您建立的連線之後，您可以執行的動作並接聽本文所述的觸發程序使用它。 


>[AZURE.INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)] 


>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="reference-for-wunderlist"></a>Wunderlist 參考
適用於版本︰ 1.0

## <a name="triggertaskdue"></a>TriggerTaskDue
工作何時到期︰ 到期清單中的任務時，將新的流程觸發程序 

```GET: /trigger/tasksdue``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|


## <a name="triggertasknew"></a>TriggerTaskNew
當建立新任務︰ 在清單中建立新的任務時，將新的流程觸發程序 

```GET: /trigger/tasksnew``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|


## <a name="triggerreminder"></a>TriggerReminder
當提醒，就會發生︰ 提醒發生時，將新的流程觸發程序 

```GET: /trigger/reminders``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|task_id|整數|沒有|查詢|無|任務識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|


## <a name="retrievelists"></a>RetrieveLists
取得清單︰ 擷取您的帳戶相關聯的清單。 

```GET: /lists``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|400|錯誤的要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="createlist"></a>CreateList
建立清單︰ 建立清單。 

```POST: /lists``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|文章| |[是]|本文|無|若要建立新清單|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|預設值|作業失敗。|


## <a name="listtasks"></a>ListTasks
取得工作︰ 擷取特定清單中的工作。 

```GET: /tasks``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|完成|布林值|沒有|查詢|無|完成|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|400|錯誤的要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="createtask"></a>CreateTask
建立工作︰ 建立工作 

```POST: /tasks``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|文章| |[是]|本文|無|若要建立新工作|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|201|建立|


## <a name="listsubtasks"></a>ListSubTasks
取得子任務︰ 擷取子任務，從特定清單或特定的任務。 

```GET: /subtasks``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|task_id|整數|沒有|查詢|無|任務識別碼|
|完成|布林值|沒有|查詢|無|完成|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|400|錯誤的要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="createsubtask"></a>CreateSubTask
建立子任務︰ 建立子任務在特定的任務 

```POST: /subtasks``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|文章| |[是]|本文|無|若要建立新的子任務|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|201|建立|


## <a name="listnotes"></a>ListNotes
取得筆記︰ 擷取特定清單或特定工作的筆記。 

```GET: /notes``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|task_id|整數|沒有|查詢|無|任務識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|400|錯誤的要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="createnote"></a>CreateNote
建立記事︰ 將記事新增至特定的任務 

```POST: /notes``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|文章| |[是]|本文|無|若要建立新的筆記|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|201|建立|


## <a name="listcomments"></a>ListComments
取得工作註解︰ 擷取特定清單或特定工作的工作註解。 

```GET: /task_comments``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|task_id|整數|沒有|查詢|無|任務識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|400|錯誤的要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="createcomment"></a>CreateComment
新增註解至任務︰ 將註解新增至特定的任務 

```POST: /task_comments``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|文章| |[是]|本文|無|若要建立新工作註解|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|201|建立|


## <a name="retrievereminders"></a>RetrieveReminders
取得提醒︰ 擷取特定清單或特定工作的提醒。 

```GET: /reminders``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|task_id|整數|沒有|查詢|無|任務識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|400|錯誤的要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="createreminder"></a>CreateReminder
設定提醒︰ 設定提醒。 

```POST: /reminders``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|文章| |[是]|本文|無|若要建立新的提醒|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|預設值|作業失敗。|


## <a name="retrievefiles"></a>RetrieveFiles
取得檔案︰ 擷取特定清單或特定工作的檔案。 

```GET: /files``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|task_id|整數|沒有|查詢|無|任務識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|作業成功|
|400|錯誤的要求|
|500|內部伺服器錯誤。 發生未知的錯誤|
|預設值|作業失敗。|


## <a name="getlist"></a>GetList
取得清單︰ 擷取特定清單 

```GET: /lists/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|清單識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="deletelist"></a>DeleteList
刪除清單︰ 刪除清單 

```DELETE: /lists/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|清單識別碼|
|修訂|整數|[是]|查詢|無|修訂|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|204|任何內容|


## <a name="updatelist"></a>UpdateList
更新清單︰ 更新特定清單 

```PATCH: /lists/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|清單識別碼|
|文章| |[是]|本文|無|[清單詳細資料|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="gettask"></a>GetTask
取得工作︰ 擷取特定的任務 

```GET: /tasks/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|識別碼|整數|[是]|路徑|無|任務識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="updatetask"></a>UpdateTask
更新任務︰ 更新特定的任務 

```PATCH: /tasks/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|識別碼|整數|[是]|路徑|無|任務識別碼|
|文章| |[是]|本文|無|工作詳細資料|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="deletetask"></a>DeleteTask
刪除工作︰ 刪除特定的任務 

```DELETE: /tasks/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|list_id|整數|[是]|查詢|無|清單識別碼|
|識別碼|整數|[是]|路徑|無|任務識別碼|
|修訂|整數|[是]|查詢|無|修訂|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|204|任何內容|


## <a name="getsubtask"></a>GetSubTask
取得子任務︰ 擷取特定的子任務 

```GET: /subtasks/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|任務識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="updatesubtask"></a>UpdateSubTask
更新為子任務︰ 更新特定的子任務 

```PATCH: /subtasks/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|任務識別碼|
|文章| |[是]|本文|無|子任務的詳細資料|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="deletesubtask"></a>DeleteSubTask
刪除子任務︰ 刪除特定的子任務 

```DELETE: /subtasks/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|任務識別碼|
|修訂|整數|[是]|查詢|無|修訂|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|204|任何內容|


## <a name="getnote"></a>GetNote
取得附註︰ 擷取特定的記事 

```GET: /notes/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|請注意識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="updatenote"></a>UpdateNote
更新附註︰ 更新特定的記事 

```PATCH: /notes/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|請注意識別碼|
|文章| |[是]|本文|無|請注意詳細資料|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="deletenote"></a>DeleteNote
刪除附註︰ 刪除特定的記事 

```DELETE: /notes/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|請注意識別碼|
|修訂|整數|[是]|查詢|無|修訂|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|204|任何內容|


## <a name="getcomment"></a>GetComment
取得工作註解︰ 擷取特定任務註解 

```GET: /task_comments/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|字串|[是]|路徑|無|註解識別碼|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="updatereminder"></a>UpdateReminder
更新提醒︰ 更新特定的提醒 

```PATCH: /reminders/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|提醒識別碼|
|文章| |[是]|本文|無|提醒詳細資料|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|200|[確定]|


## <a name="deletereminder"></a>DeleteReminder
刪除提醒︰ 刪除特定的提醒 

```DELETE: /reminders/{id}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|識別碼|整數|[是]|路徑|無|提醒的識別碼。|
|修訂|整數|[是]|查詢|無|修訂|

#### <a name="response"></a>回應

|名稱|描述|
|---|---|
|204|任何內容|


## <a name="object-definitions"></a>物件定義 

### <a name="list"></a>清單


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|created_at|字串|無 |
|標題|字串|無 |
|list_type|字串|無 |
|類型|字串|無 |
|修訂|整數|無 |



### <a name="createdlist"></a>CreatedList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|created_at|字串|無 |
|標題|字串|無 |
|修訂|整數|無 |
|類型|字串|無 |



### <a name="task"></a>任務


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|assignee_id|整數|無 |
|assigner_id|整數|無 |
|created_at|字串|無 |
|created_by_id|整數|無 |
|due_date|字串|無 |
|list_id|整數|無 |
|修訂|整數|無 |
|起子頭|布林值|無 |
|標題|字串|無 |



### <a name="subtask"></a>子任務


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|task_id|整數|無 |
|created_at|字串|無 |
|created_by_id|整數|無 |
|修訂|字串|無 |
|標題|字串|無 |



### <a name="note"></a>附註


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|task_id|整數|無 |
|內容|字串|無 |
|created_at|字串|無 |
|updated_at|字串|無 |
|修訂|整數|無 |



### <a name="comment"></a>註解


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|task_id|整數|無 |
|修訂|整數|無 |
|文字|字串|無 |
|類型|字串|無 |
|created_at|字串|無 |



### <a name="reminder"></a>提醒


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|日期|字串|無 |
|task_id|整數|無 |
|修訂|整數|無 |
|類型|字串|無 |
|created_at|字串|無 |
|updated_at|字串|無 |



### <a name="createdreminder"></a>CreatedReminder


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|日期|字串|無 |
|task_id|整數|無 |
|修訂|整數|無 |
|created_at|字串|無 |
|updated_at|字串|無 |



### <a name="file"></a>檔案


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|識別碼|整數|無 |
|url|字串|無 |
|task_id|整數|無 |
|list_id|整數|無 |
|user_id|整數|無 |
|file_name|字串|無 |
|content_type|字串|無 |
|file_size|整數|無 |
|local_created_at|字串|無 |
|created_at|字串|無 |
|updated_at|字串|無 |
|類型|字串|無 |
|修訂|整數|無 |



### <a name="newtask"></a>NewTask


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|list_id|整數|[是] |
|標題|字串|[是] |
|assignee_id|整數|無 |
|完成|布林值|無 |
|recurrence_type|字串|無 |
|recurrence_count|整數|無 |
|due_date|字串|無 |
|起子頭|布林值|無 |



### <a name="newlist"></a>NewList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|標題|字串|[是] |



### <a name="newsubtask"></a>NewSubtask


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|list_id|整數|[是] |
|task_id|整數|[是] |
|標題|字串|[是] |
|完成|布林值|無 |



### <a name="newnote"></a>NewNote


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|list_id|整數|[是] |
|task_id|整數|[是] |
|內容|字串|[是] |



### <a name="newcomment"></a>NewComment


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|list_id|整數|[是] |
|task_id|整數|[是] |
|文字|字串|[是] |



### <a name="newreminder"></a>NewReminder


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|list_id|整數|[是] |
|task_id|整數|[是] |
|日期|字串|[是] |



### <a name="updatetask"></a>UpdateTask


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|修訂|整數|無 |
|標題|字串|無 |
|assignee_id|整數|無 |
|完成|布林值|無 |
|recurrence_type|字串|無 |
|recurrence_count|整數|無 |
|due_date|字串|無 |
|起子頭|布林值|無 |



### <a name="updatelist"></a>UpdateList


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|修訂|整數|無 |
|標題|字串|無 |



### <a name="updatesubtask"></a>UpdateSubtask


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|修訂|整數|無 |
|標題|字串|無 |
|完成|布林值|無 |



### <a name="updatenote"></a>UpdateNote


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|修訂|整數|無 |
|內容|字串|無 |



### <a name="updatereminder"></a>UpdateReminder


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|日期|字串|無 |
|修訂|整數|無 |


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)