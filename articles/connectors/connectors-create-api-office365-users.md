<properties
    pageTitle="邏輯應用程式中新增 Office 365 使用者連接器 |Microsoft Azure"
    description="Office 365 使用者連接器與 REST API 參數的概觀"
    services=""    
    documentationCenter=""     
    authors="msftman"    
    manager="erikre"    
    editor="" 
    tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-office-365-users-connector"></a>開始使用 Office 365 使用者連接器

連線至 Office 365 使用者，以取得設定檔、 搜尋使用者及其他功能。 

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2015年-08-01-預覽結構描述版本。

使用 Office 365 的使用者，您可以︰

- 建立您的商務流程根據您收到來自 Office 365 使用者的資料。 
- 使用動作，以取得直屬，取得管理員的使用者設定檔，及其他內容。 這些動作獲得回應，然後再輸出可用的其他動作。 例如，取得人員直接報告，然後取得這項資訊及更新 SQL Azure 資料庫。 

若要新增作業邏輯應用程式中，請參閱[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

## <a name="triggers-and-actions"></a>觸發程序及動作

Office 365 使用者連接器有下列可用的動作。 有任何引動程序。

| 觸發程序 | 動作|
| --- | --- |
|無 | <ul><li>取得管理員</li><li>取得我的設定檔</li><li>取得直接報告</li><li>取得使用者設定檔</li><li>搜尋使用者</li></ul>|

所有的連接器支援 JSON 和 XML 格式的資料。 


## <a name="create-a-connection-to-office-365-users"></a>建立 Office 365 使用者的連線

當您新增此連接器至您的邏輯應用程式時，您必須登入您的 Office 365 的使用者帳戶並允許邏輯相關應用程式連線至您的帳戶。

>[AZURE.INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]

您建立的連線之後，您輸入的 Office 365 使用者屬性，例如使用者識別碼。 **REST API 參考**本主題中說明這些屬性。

>[AZURE.TIP] 您可以在其他邏輯應用程式中使用此相同的 Office 365 的使用者連線。


## <a name="office-365-users-rest-api-reference"></a>Office 365 使用者 REST API 參照
適用於版本︰ 1.0。

### <a name="get-my-profile"></a>取得我的設定檔 
擷取目前使用者的設定檔。  
```GET: /users/me``` 

沒有這個呼叫參數。

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


### <a name="get-user-profile"></a>取得使用者設定檔 
擷取特定的使用者設定檔。  
```GET: /users/{userId}``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|使用者識別碼|字串|[是]|路徑|無|使用者主體名稱或電子郵件識別碼|

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


### <a name="get-manager"></a>取得管理員 
擷取指定之使用者的管理員的使用者設定檔。  
```GET: /users/{userId}/manager``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|使用者識別碼|字串|[是]|路徑|無|使用者主體名稱或電子郵件識別碼|

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



### <a name="get-direct-reports"></a>取得直接報告 
取得直接報告。  
```GET: /users/{userId}/directReports``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|使用者識別碼|字串|[是]|路徑|無|使用者主體名稱或電子郵件識別碼|

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



### <a name="search-for-users"></a>搜尋使用者 
擷取搜尋結果的使用者設定檔。  
```GET: /users``` 

| 名稱| 資料類型|所需|位於|預設值|描述|
| ---|---|---|---|---|---|
|searchTerm|字串|沒有|查詢|無|搜尋字串 (適用於︰ 顯示名稱、 名字、 姓氏、 郵件、 郵件暱稱與使用者主體名稱)|

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



## <a name="object-definitions"></a>物件定義

#### <a name="user-user-model-class"></a>使用者︰ 使用者模型類別

|屬性名稱 | 資料類型 |所需
|---|---|---|
|顯示名稱|字串|沒有|
|GivenName|字串|沒有|
|姓氏|字串|沒有|
|郵件|字串|沒有|
|MailNickname|字串|沒有|
|TelephoneNumber|字串|沒有|
|AccountEnabled|布林值|沒有|
|識別碼|字串|[是]
|UserPrincipalName|字串|沒有|
|部門|字串|沒有|
|JobTitle|字串|沒有|
|mobilePhone|字串|沒有|


## <a name="next-steps"></a>後續步驟

[建立邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)。

回到[Api 清單](apis-list.md)。

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG
