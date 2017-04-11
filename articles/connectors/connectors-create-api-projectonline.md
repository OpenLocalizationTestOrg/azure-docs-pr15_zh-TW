<properties
pageTitle="ProjectOnline |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 Project Online 是非常有彈性的線上解決方案，專案產品組合管理 (PPM) 與 Microsoft 的每日例行工作。 傳送到 Office 365，Project Online 可讓組織快速開始使用強大的專案管理功能計劃、 排列優先順序，及管理專案和專案產品組合投資，無論身在何處幾乎任何裝置上。"
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

# <a name="get-started-with-the-projectonline-connector"></a>快速入門 ProjectOnline 連接器

Project Online 是非常有彈性的線上解決方案，專案產品組合管理 (PPM) 與 Microsoft 的每日例行工作。 傳送到 Office 365，Project Online 可讓組織快速開始使用強大的專案管理功能計劃、 排列優先順序，及管理專案和專案產品組合投資，無論身在何處幾乎任何裝置上。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

ProjectOnline 連接器可為的動作;有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 ProjectOnline 連接線具有下列的動作及/或可用的觸發程序︰

### <a name="projectonline-actions"></a>ProjectOnline 動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[ListProjects](connectors-create-api-projectonline.md#listprojects)|列出您的 project online 網站中的專案|
|[CreateProject](connectors-create-api-projectonline.md#createproject)|您的 project online 網站上建立新專案|
|[CreateTask](connectors-create-api-projectonline.md#createtask)|您在 project 中建立新工作|
|[CreateResource](connectors-create-api-projectonline.md#createresource)|您的 project online 網站上建立企業資源|
|[ListTasks](connectors-create-api-projectonline.md#listtasks)|列出專案中的已發佈的工作|
|[CheckoutProject](connectors-create-api-projectonline.md#checkoutproject)|簽出您的網站中的專案|
|[PublishProject](connectors-create-api-projectonline.md#publishproject)|存回的發佈和現有的專案，在您的網站|
### <a name="projectonline-triggers"></a>ProjectOnline 引動程序
您可以接聽這些事件︰

|觸發程序 | 描述|
|--- | ---|
|建立新的專案時，|每次建立新專案時，觸發程序的流程|
|建立新的資源時|建立新的資源時，將新的流程觸發程序|
|建立新任務的時機|建立新的任務時，將流量的觸發程序|


## <a name="create-a-connection-to-projectonline"></a>建立 ProjectOnline 的連線
若要建立 ProjectOnline 邏輯應用程式，您必須先建立的**連線**，然後提供下列屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供 ProjectOnline 認證|

>[AZURE.INCLUDE [Steps to create a connection to ProjectOnline](../../includes/connectors-create-api-projectonline.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="reference-for-projectonline"></a>ProjectOnline 參考
適用於版本︰ 1.0

## <a name="onnewproject"></a>OnNewProject
建立新的專案時，︰ 每次建立新專案時，觸發程序的流程 

```GET: /trigger/_api/ProjectData/Projects``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewresource"></a>OnNewResource
建立新的資源時︰ 建立新的資源時，將新的流程觸發程序 

```GET: /trigger/_api/ProjectData/Resources``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="onnewtask"></a>OnNewTask
建立新任務的時機︰ 觸發流程時建立新的任務 

```GET: /trigger/_api/ProjectData/Tasks``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="listprojects"></a>ListProjects
清單專案︰ 列出您的 project online 網站中的專案 

```GET: /_api/ProjectServer/Projects``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|

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


## <a name="createproject"></a>CreateProject
建立新專案︰ 您的 project online 網站上建立新專案 

```POST: /_api/ProjectServer/Projects``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|
|專案| |[是]|本文|無|若要建立新專案|

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


## <a name="createtask"></a>CreateTask
建立新工作︰ 您在 project 中建立新工作 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Tasks/Add``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|
|project_id|字串|[是]|路徑|無|若要新增任務至專案的唯一識別碼|
|任務| |[是]|本文|無|若要新增至專案的新工作|

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


## <a name="createresource"></a>CreateResource
建立新的資源︰ 在 project online 網站建立企業資源 

```POST: /_api/ProjectServer/EnterpriseResources``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|
|資源| |[是]|本文|無|若要新增專案至新的企業資源|

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


## <a name="listtasks"></a>ListTasks
列出工作︰ 列出專案中的已發佈的工作 

```GET: /_api/ProjectServer/Projects('{project_id}')/Tasks``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|
|project_id|字串|[是]|路徑|無|擷取任務的唯一識別碼的專案|

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


## <a name="checkoutproject"></a>CheckoutProject
取出專案︰ 取出的專案，在您的網站 

```POST: /_api/ProjectServer/Projects('{project_id}')/checkOut``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|
|project_id|字串|[是]|路徑|無|若要新增任務至專案的唯一識別碼|

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


## <a name="publishproject"></a>PublishProject
存回及發佈專案︰ 存回的發佈及現有專案，在您的網站 

```POST: /_api/ProjectServer/Projects('{project_id}')/Draft/Publish(true)``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|siteUrl|字串|[是]|查詢|無|根專案網站的網站 url (範例︰ https://sampletenant.sharepoint.com/teams/sampleteam)|
|project_id|字串|[是]|路徑|無|若要存回專案的唯一識別碼|

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

### <a name="triggerprojectswrapper"></a>TriggerProjectsWrapper


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="triggerproject"></a>TriggerProject


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|ProjectStartDate|字串|無 |
|ProjectFinishDate|字串|無 |
|ProjectCreatedDate|字串|無 |
|ProjectId|字串|無 |
|ProjectModifiedDate|字串|無 |
|ProjectType|整數|無 |
|在專案名稱|字串|無 |



### <a name="triggerresourceswrapper"></a>TriggerResourcesWrapper


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="triggerresource"></a>TriggerResource


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|預設|字串|無 |
|ResourceBaseCalendar|字串|無 |
|ResourceBookingType|整數|無 |
|ResourceCanLevel|布林值|無 |
|ResourceCostPerUse|數字|無 |
|ResourceCreatedDate|字串|無 |
|ResourceEarliestAvailableFrom|字串|無 |
|ResourceEmail|字串|無 |
|ResourceInitials|字串|無 |
|ResourceIsActive|布林值|無 |
|ResourceIsGeneric|布林值|無 |
|ResourceLatestAvailableTo|字串|無 |
|ResourceModifiedDate|字串|無 |
|ResourceName|字串|無 |
|ResourceStatsuName|字串|無 |
|ResourceType|整數|無 |
|TypeDescription|字串|無 |
|TypeName|字串|無 |



### <a name="triggertaskswrapper"></a>TriggerTasksWrapper


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="triggertask"></a>TriggerTask


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|ProjectId|字串|無 |
|預設|字串|無 |
|在專案名稱|字串|無 |
|工作名稱|字串|無 |
|TaskCreatedDate|字串|無 |
|TaskModifieddate|字串|無 |
|TaskStartDate|字串|無 |
|TaskFinishDate|字串|無 |
|TaskPriority|整數|無 |
|TaskIsActive|布林值|無 |



### <a name="newproject"></a>[新增專案]


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|名稱|字串|[是] |
|描述|字串|無 |
|開始|字串|無 |



### <a name="newreource"></a>NewReource


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|名稱|字串|[是] |
|IsBudget|布林值|無 |
|IsGeneric|布林值|無 |
|IsInactive|布林值|無 |



### <a name="project"></a>專案


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|ApprovedStart|字串|無 |
|ApprovedEnd|字串|無 |
|CheckedOutDate|字串|無 |
|CheckOutDescription|字串|無 |
|CheckOutId|字串|無 |
|CreatedDate|字串|無 |
|識別碼|字串|無 |
|IsCheckedOut|布林值|無 |
|LastPublishedDate|字串|無 |
|LastSavedDate|字串|無 |
|OptimizerDecision|整數|無 |
|PlannerDecision|整數|無 |
|ProjectType|整數|無 |
|名稱|字串|無 |
|WinprojVersion|字串|無 |



### <a name="projectswrapper"></a>ProjectsWrapper


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="newtask"></a>NewTask


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|參數|未定義|[是] |



### <a name="taskparameters"></a>TaskParameters


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|名稱|字串|[是] |
|備忘稿|字串|無 |
|開始|字串|無 |
|持續時間|字串|無 |



### <a name="enterpriseresource"></a>EnterpriseResource


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|CanLevel|布林值|無 |
|程式碼|字串|無 |
|CostAccrual|整數|無 |
|CostCenter|字串|無 |
|建立|字串|無 |
|DefaultBookingType|整數|無 |
|電子郵件|字串|無 |
|ExternalId|字串|無 |
|群組|字串|無 |
|HireDate|字串|無 |
|識別碼|字串|無 |
|縮寫|字串|無 |
|IsActive|布林值|無 |
|IsBudget|布林值|無 |
|IsCheckedOut|布林值|無 |
|IsGeneric|布林值|無 |
|IsTeam|布林值|無 |
|MaterialLabel|字串|無 |
|修改|字串|無 |
|名稱|字串|無 |
|語音|字串|無 |
|ResourceType|整數|無 |
|TerminationDate|字串|無 |



### <a name="taskswrapper"></a>TasksWrapper


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|值|陣列|無 |



### <a name="task"></a>任務


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|建立|字串|無 |
|修改|字串|無 |
|開始|字串|無 |
|完成|字串|無 |
|名稱|字串|無 |
|識別碼|字串|無 |
|優先順序|整數|無 |
|PercentComplete|整數|無 |
|備忘稿|字串|無 |
|連絡人|字串|無 |


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)