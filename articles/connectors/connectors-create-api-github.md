<properties
pageTitle="GitHub |Microsoft Azure"
description="Azure 應用程式服務建立邏輯應用程式。 GitHub 是網頁式給存放庫主機服務。 提供的所有分散式的修訂控制項與來源的程式碼管理 (SCM) 功能給以及新增自己的功能。"
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

# <a name="get-started-with-the-github-connector"></a>快速入門 GitHub 連接器

GitHub 是網頁式給存放庫主機服務。 提供的所有分散式的修訂控制項與來源的程式碼管理 (SCM) 功能給以及新增自己的功能。

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。 

您可以開始時，現在建立邏輯應用程式，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

為的動作; 可 GitHub 連接器有觸發程序。 所有的連接器支援 JSON 和 XML 格式的資料。 

 GitHub 連接線具有下列的動作及/或可用的觸發程序︰

### <a name="github-actions"></a>GitHub 動作
您可以採取下列動作︰

|巨集指令|描述|
|--- | ---|
|[CreateIssue](connectors-create-api-github.md#createissue)|建立問題|
### <a name="github-triggers"></a>GitHub 引動程序
您可以接聽這些事件︰

|觸發程序 | 描述|
|--- | ---|
|開啟問題時|開啟問題|
|關閉問題時|關閉問題|
|當指派問題|指派問題|


## <a name="create-a-connection-to-github"></a>建立 GitHub 的連線
若要建立 GitHub 邏輯應用程式，您必須先建立的**連線**，然後提供下列屬性的詳細資料︰ 

|屬性| 所需|描述|
| ---|---|---|
|權杖|[是]|提供 GitHub 認證|
您建立的連線之後，您可以執行的動作並接聽本文所述的觸發程序使用它。 

>[AZURE.INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此連線。

## <a name="reference-for-github"></a>GitHub 參考
適用於版本︰ 1.0

## <a name="createissue"></a>CreateIssue
建立問題︰ 建立有問題 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|repositoryOwner|字串|[是]|路徑|無|存放庫擁有者|
|repositoryName|字串|[是]|路徑|無|存放庫的名稱|
|issueBasicDetails| |[是]|本文|無|問題詳細資料|

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


## <a name="issueopened"></a>IssueOpened
開啟問題時︰ 開啟問題 

```GET: /trigger/issueOpened``` 

沒有這個呼叫參數
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


## <a name="issueclosed"></a>IssueClosed
當關閉問題︰ 問題已關閉 

```GET: /trigger/issueClosed``` 

沒有這個呼叫參數
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


## <a name="issueassigned"></a>IssueAssigned
當指派問題︰ 分派的問題 

```GET: /trigger/issueAssigned``` 

沒有這個通話的參數
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

### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|標題|字串|[是] |
|本文|字串|[是] |
|assignee|字串|[是] |



### <a name="issuedetailsmodel"></a>IssueDetailsModel


| 屬性名稱 | 資料類型 | 所需 |
|---|---|---|
|標題|字串|[是] |
|本文|字串|[是] |
|assignee|字串|[是] |
|數字|字串|無 |
|狀態|字串|無 |
|created_at|字串|無 |
|repository_url|字串|無 |


## <a name="next-steps"></a>後續步驟
[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)