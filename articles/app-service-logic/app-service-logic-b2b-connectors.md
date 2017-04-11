<properties 
    pageTitle="企業對企業連接器與邏輯應用程式中的 API 應用程式 |Microsoft Azure" 
    description="瞭解如何建立及設定 EDI、 EDIFACT、 AS2 和 TPM 連接器;microservices 架構" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>企業對企業連接器與 API 應用程式

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

邏輯應用程式包含許多 BizTalk API 應用程式的重要整合環境。 這些 API 應用程式為基礎的概念與 BizTalk 伺服器中使用的工具，但現在可以使用邏輯應用程式的一部分。 

這些 API 應用程式的類別是企業企業 B2B API 應用程式。 使用這些 B2B API 應用程式，您可以輕鬆地新增合作夥伴、 建立協議及執行時的所有項目會內部部署使用 EDI、 AS2 和 EDIFACT。  

這些 B2B API 應用程式提供 「 觸發程序 」 和 「 動作 」 功能。 觸發程序開始新的執行個體，根據特定的事件，例如 X12 到達合作夥伴郵件。 動作收到 X12 之後將結果，就像訊息，然後傳送郵件 AS2。


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>什麼是商務商務連接器或 API 應用程式
企業對企業 (B2B) 功能會包含現有、 預先建立的 API 應用程式，可讓不同的公司、 部門、 應用程式，等，以使用 AS2、 EDI 和 EDIFACT 進行通訊。 

包含 B2B API 應用程式︰ 

連接器或 API 應用程式 | 描述
--- | ---
BizTalk 交易夥伴管理 | 建立使用協力廠商和合約的企業對企業 (B2B) 關聯的 API 應用程式。 這些關聯性利用 AS2 EDIFACT，與 X12 通訊協定。<br/><br/>TPM API 應用程式是 AS2 連接器，以及 X12 或 EDIFACT API 應用程式的基本需求。 
AS2 連接器 | 接收並傳送訊息使用 AS2 傳輸連接器。 連接器傳輸資料安全地且確實透過網際網路。
BizTalk EDIFACT | 接收並傳送訊息使用 EDIFACT API 應用程式。 EDIFACT 也通常稱為取消/EDIFACT （美國國家/電子資料交換的管理、 商務和傳輸），然後廣泛產業。
BizTalk X12 | 接收並傳送訊息使用 X12 API 應用程式通訊協定。 X12 也通常稱為 ASC X 12 （合法標準委員會 X12），然後廣泛產業。 


您可以使用這些 API 應用程式，來完成不同 EDI 訊息的工作。 例如，使用 AS2 連接器，您可以安全地收發不同類型的訊息 (EDI、 XML、 一般檔案等) 給客戶，您公司內除法等人力資源，或使用 AS2 的任何人。 

您可以建立您的需求並輕鬆地建立任何數量的 API 應用程式。 您也可以重複使用多個案例或工作流程中的單一 API 應用程式。

您可以透過以下無須撰寫程式碼。 我們開始吧。 


## <a name="requirements-to-get-started"></a>若要開始的需求
當您建立 B2B API 應用程式時，有一些所需的資源。 這些項目必須由您先建立可用於其他 API 應用程式。 這些需求包括︰ 

要求 | 描述
--- | ---
Azure SQL 資料庫 | 儲存 B2B 項目，包括協力廠商、 結構、 憑證和 agreeements。 每個 B2B API 應用程式需要自己 Azure SQL 資料庫。 <br/><br/>**附註**複製到這個資料庫的連線字串。<br/><br/>[建立 Azure SQL 資料庫](../sql-database/sql-database-get-started.md)
Azure Blob 儲存體容器 | 儲存訊息內容，AS2 封存啟用時。 如果您不需要 AS2 封存的郵件，就不需要存放容器。 <br/><br/>**附註**如果您要啟用封存，請至此 Blob 儲存體複製連線字串。<br/><br/>[關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)
服務匯流排命名空間和其鍵值 | 儲存 X12 和 EDIFACT 批次資料。 如果您不需要批次，就不需要的服務匯流排命名空間。<br/><br/>**附註**如果您要啟用批次，複製下列值。<br/><br/>[建立服務匯流排命名空間](http://msdn.microsoft.com/library/azure/hh690931.aspx)
TPM 執行個體 | BizTalk 交易合作夥伴管理 (TPM) 執行個體，才能建立 AS2 連接器並 X12 或 EDIFACT API 應用程式。 當您建立 TPM API 應用程式時，您會建立 TPM 執行個體。 <br/><br/>**附註**知道您 TPM API 應用程式的名稱。 


## <a name="create-the-api-apps"></a>建立 API 應用程式
使用 [Azure 入口網站，或使用 REST Api，您可以建立 B2B API 應用程式。 


### <a name="create-the-api-apps-using-rest-apis"></a>建立使用 REST Api 的 API 應用程式
[請參閱如何使用 REST Api 文件。](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Azure 入口網站中建立 B2B API 應用程式
在 Azure 入口網站，您可以在建立邏輯應用程式、 Web 應用程式或行動應用程式時，建立 B2B API 應用程式。 或者，您可以建立一個使用自己的刀。 這兩個方法是簡單，因此，取決於您的需求或喜好設定]。 部分使用者想要先建立所有 B2B API 應用程式的特定屬性。 然後，建立邏輯應用程式/Web 應用程式/行動應用程式，並新增您建立 B2B API 應用程式。  

下列步驟建立使用 API 應用程式刀 B2B API 應用程式。


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>建立 BizTalk 交易合作夥伴管理 (TPM) API 應用程式

> [AZURE.NOTE] BizTalk 交易合作夥伴管理 (TPM) 執行個體，才能建立 AS2 連接器並 X12 或 EDIFACT API 應用程式。 當您建立 TPM API 應用程式時，您會建立 TPM 執行個體。

下列步驟建立 TPM 執行個體︰

1. Azure 入口網站中 Startboard （首頁），選取 [ **Marketplace**]。 **API 應用程式**會列出所有現有的 API 應用程式和連接器。 也可以**搜尋**特定 B2B API 應用程式。
2. 選取 [ **BizTalk 交易夥伴管理**]。 在新刀中，選取 [**建立**。 
3. 輸入的內容︰ 

    屬性 | 描述
--- | ---
名稱 | 輸入任何 TPM 執行個體名稱。 例如，您可以將其命名*AccountsPayableTPM*。
封裝設定 | 輸入您所建立的 Azure SQL 資料庫 ADO.NET**資料庫連線字串**。 <br/><br/>當您複製連線字串時，密碼不會加入至連線字串。 請務必輸入連線字串中的密碼。
應用程式服務計劃 | 列出您的付款計劃。 如果您需要更多或較少的資源，您可以變更它。
價格層 | 列出您 Azure 訂閱中的 [價格] 類別的唯讀的屬性。 
資源群組 | 建立新的範本或使用現有的群組。 所有的 API 應用程式和連接器邏輯應用程式、 Web 應用程式，與 Mobile 應用程式必須相同的 [資源] 群組中。 <br/><br/>[使用資源群組](../azure-resource-manager/resource-group-overview.md)說明此屬性。 
訂閱 | 列出目前訂閱的唯讀的屬性。
位置 | 地理位置主控您 Azure 服務。 
新增至 Startboard | 選取此選項將 B2B API 應用程式新增至您的右 （首頁）。

4. 選取 [**建立**]。 

建立 TPM API 應用程式 （TPM 執行個體） 之後，然後您可以建立 AS2 連接器，及/或 X12 或 EDIFACT API 應用程式。 


#### <a name="create-the-as2-connector"></a>建立 AS2 連接器

1. Azure 入口網站中 Startboard （首頁），選取 [ **Marketplace**]。 **API 應用程式**會列出所有現有的 API 應用程式和連接器。 也可以**搜尋**特定 B2B API 應用程式。
2. 選取 [ **AS2 連接器**]。 在新刀中，選取 [**建立**。 
3. 輸入的內容︰ 

    屬性 | 描述
--- | ---
名稱 | 輸入任何 AS2 連接器名稱。 例如，您可以將其命名*AS2Connector*。
封裝設定 | 輸入特定設定至該 API 應用程式，如 TPM 執行個體名稱。 <br/><br/>本主題的特定屬性，請參閱[新增 AS2 封裝設定](#AddAS2Conn)。 
應用程式服務計劃 | 列出您的付款計劃。 如果您需要更多或較少的資源，您可以變更它。
價格層 | 列出您 Azure 訂閱中的 [價格] 類別的唯讀的屬性。 
資源群組 | 建立新的範本或使用現有的群組。 [使用資源群組](../azure-resource-manager/resource-group-overview.md)說明此屬性。 
訂閱 | 列出目前訂閱的唯讀的屬性。
位置 | 地理位置主控您 Azure 服務。 
新增至 Startboard | 選取此選項將 B2B API 應用程式新增至您的右 （首頁）。

    **<a name="AddAS2Conn"></a>AS2 連接器封裝設定**

    屬性 | 描述
--- | --- 
資料庫連線字串 | 輸入您所建立的 Azure SQL 資料庫 ADO.NET 連線字串。 當您複製連線字串時，密碼不會加入至連線字串。 請務必輸入連線字串中的密碼，然後再貼。
啟用封存內送郵件 | 選用。 啟用這個屬性來儲存郵件內容，自合作夥伴傳入 AS2 訊息。 
Azure Blob 儲存體連線字串  | 輸入您所建立的 Azure Blob 儲存體容器中的連接字串。 啟用封存時，編碼及解碼郵件會儲存在此儲存容器中。
TPM 執行個體名稱 | 輸入**BizTalk 交易夥伴管理**您先前建立的 API 應用程式的名稱。 當您建立 AS2 連接器時，此連接器執行僅在此特定 TPM 執行個體的 AS2 合約。

4. 選取 [**建立**]。 


#### <a name="create-the-x12-or-edifact-api-apps"></a>建立 X12 或 EDIFACT API 應用程式

1. Azure 入口網站中 Startboard （首頁），選取 [ **Marketplace**]。 **API 應用程式**會列出所有現有的 API 應用程式和連接器。 也可以**搜尋**特定 B2B API 應用程式。
2. 選取**BizTalk X 12**或**BizTalk EDIFACT**。 在新刀中，選取 [**建立**。 
3. 輸入的內容︰ 

    屬性 | 描述
--- | ---
名稱 | 輸入任何 B2B API 應用程式的名稱。 例如，您可以將其命名*EDI850APIApp*。
封裝設定 | 輸入特定設定至該 API 應用程式，如 TPM 執行個體名稱。 <br/><br/>本主題的特定屬性，請參閱[X12 或 EDIFACT 封裝設定](#AddX12)。 
應用程式服務計劃 | 列出您的付款計劃。 如果您需要更多或較少的資源，您可以變更它。
價格層 | 列出您 Azure 訂閱中的 [價格] 類別的唯讀的屬性。 
資源群組 | 建立新的範本或使用現有的群組。 [使用資源群組](../azure-resource-manager/resource-group-overview.md)說明此屬性。 
訂閱 | 列出目前訂閱的唯讀的屬性。
位置 | 地理位置主控您 Azure 服務。 
新增至 Startboard | 選取此選項將 B2B API 應用程式新增至您的右 （首頁）。

    **<a name="AddX12"></a>X12 和 EDIFACT API 應用程式封裝設定**  

    屬性 | 描述
--- | --- 
資料庫連線字串 | 輸入您所建立的 Azure SQL 資料庫 ADO.NET 連線字串。 當您複製連線字串時，密碼不會加入至連線字串。 請務必輸入連線字串中的密碼，然後再貼。
服務匯流排命名空間 | 輸入您所建立的服務匯流排命名空間。 需要啟用時才批次。 
服務匯流排命名空間共用便捷鍵名稱 | 輸入服務匯流排命名空間您建立的便捷鍵。 需要啟用時才批次。 
服務匯流排命名空間共用便捷鍵值 | 輸入服務匯流排命名空間便捷鍵您建立的值。 需要啟用時才批次。 
TPM 執行個體名稱 | 輸入**BizTalk 交易夥伴管理**您先前建立的 API 應用程式的名稱。 當您建立 X12 或 EDIFACT API 應用程式時，此 API 應用程式執行僅在此特定 TPM 執行個體的 X12/EDFIACT 合約。

4. 選取 [**建立**]。 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>新增您的合作夥伴、 協議、 憑證和結構描述 
在 Azure 入口網站中，開啟 [TPM API 應用程式]。 在 [**元件**] 區段中，新增您的合作夥伴、 協議、 憑證和結構描述。 

您也可以新增合約您 AS2 連接器，X12 API 應用程式]，然後 EDIFACT API 應用程式。 


## <a name="monitor-your-api-apps"></a>監控您的 API 應用程式
在 Azure 入口網站中，開啟 [TPM API 應用程式]。 在 [**作業**] 區段中，您可以檢視不同的管理作業。 例如，您可以︰

- 檢視資訊和事件
- 檢視工作程序 (w3wp) 的記憶體使用情況和執行緒字數統計
- 檢視應用程式與網路伺服器記錄

更多在[監視器邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)。


## <a name="add-the-api-apps-to-your-application"></a>將 API 應用程式新增至您的應用程式 
Microsoft Azure 應用程式服務公開可以使用這些 B2B API 應用程式的不同的應用程式類型。 您可以建立新的或現有 B2B API 應用程式加入邏輯應用程式、 行動應用程式或 Web 應用程式。 

在您的應用程式，只會自動從圖庫選取 B2B API 應用程式，新增至您的應用程式。  

> [AZURE.IMPORTANT] 若要新增連接器與您先前建立的 API 應用程式，建立相同的 [資源] 群組中的邏輯應用程式、 行動應用程式或 Web 應用程式。 

下列步驟將 B2B API 應用程式新增至邏輯應用程式、 行動應用程式或 Web 應用程式中︰ 

1. Azure 入口網站中 Startboard （首頁），移至**市集**]，並搜尋您邏輯、 行動電話或 Web 應用程式。 

    如果您要建立新的應用程式，搜尋邏輯應用程式、 行動應用程式或 Web 應用程式。 選取應用程式，然後在新刀中，選取 [**建立**]。 [建立邏輯應用程式](app-service-logic-create-a-logic-app.md)中列出的步驟。 

2. 開啟您的應用程式，然後選取 [**觸發程序和動作**。 

3. 從**圖庫**中，選取 [B2B API 應用程式，自動新增至您的應用程式。 您也可以建立新的 B2B API 應用程式。

    > [AZURE.IMPORTANT] AS2 連接器，X12 EDIFACT API 應用程式要求 TPM 執行個體。 如果您要建立新的 B2B API 應用程式，首先，建立 TPM API 應用程式的並建立 AS2 連接線 X12 API 應用程式或 EDIFACT API 應用程式。 

4. 選取**[確定**] 儲存變更。 

>[AZURE.NOTE] 若要開始使用 Azure 邏輯應用程式之前註冊 Azure 帳戶，請[嘗試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)。 您可以立即建立短暫入門邏輯應用程式。 必要; 沒有信用卡沒有承諾。

## <a name="more-b2b-resources"></a>更多 B2B 資源

[建立 B2B 程序](app-service-logic-create-a-b2b-process.md)<br/>
[建立交易的合作夥伴合約](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>閱讀關於邏輯應用程式與 Web 應用程式
[什麼是邏輯應用程式？](app-service-logic-what-are-logic-apps.md)<br/>
[網站與 Azure 應用程式服務中的 Web 應用程式](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>更多的連接器

[連接器與 API 的應用程式清單](app-service-logic-connectors-list.md)<br/><br/>
[什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md) 
