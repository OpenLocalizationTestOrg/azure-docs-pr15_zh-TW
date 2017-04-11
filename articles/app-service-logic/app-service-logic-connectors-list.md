<properties
    pageTitle="使用連接器和 API 應用程式的清單 |Microsoft Azure 應用程式服務"
    description="瞭解連接器和 Azure 應用程式服務中的 API 應用程式"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="mandia"/>


# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>在您的邏輯應用程式中使用的連接器和 API 應用程式的清單
>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽架構版本。 邏輯應用程式一般可用性 (GA) 版本，請參閱[新增連接線清單](../connectors/apis-list.md)。

進一步瞭解所有可用的連接器，以邏輯應用程式中使用 Microsoft 所建立的 API 應用程式。

價格資訊和清單的什麼是隨附於每個服務層級，請參閱[Azure 應用程式服務價格](https://azure.microsoft.com/pricing/details/app-service/)。

> [AZURE.NOTE] 若要開始使用邏輯應用程式註冊 Azure 帳戶之前，請移至[試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)。 應用程式服務中，可以立即建立短暫入門邏輯應用程式。 必要; 沒有信用卡沒有承諾。

## <a name="core-connectors"></a>核心連接器
下表列出所有可用的連接器和 API 應用程式由 Microsoft 建立，可為核心連接器︰

名稱 | 描述
--- | ---
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | 若要將文字翻譯為另一種語言的使用 Bing。
[HTTP](app-service-logic-connector-http.md) | HTTP 接聽程式會開啟做為 HTTP 伺服器與接聽傳入 HTTP 或 HTTPS 要求的端點。 HTTP 動作不需要 API 應用程式，並原本即支援邏輯應用程式。
[可寬延時間](app-service-logic-connector-slack.md) | 連線至可寬延時間，或張貼訊息可寬延時間的頻道。


## <a name="enterprise-integration-connectors"></a>企業整合連接器
下表列出所有可用連接器與企業整合連接器提供 Microsoft 所建立的 API 應用程式︰

名稱  | 描述
------------- | -------------
[BizTalk 規則](app-service-logic-use-biztalk-rules.md) | 使用 BizTalk 規則來定義和控制在組織內的商務邏輯。 可以更新商務原則，而不重新編譯或不重新部署相關聯的應用程式。
[BizTalk XPath 抽選程式](app-service-logic-xpath-extract.md) | 尋找並從 XML 內容會根據您所選擇的 XPath 擷取資料。
[DB2 連接器](app-service-logic-connector-db2.md) | 連線到 IBM DB2 資料庫內部部署和 Azure 虛擬機器上執行 Windows 作業系統。 可以將 Web API 和 OData API 作業對應 Informix 結構化查詢語言命令。 <br/><br/>沒有觸發程序。 動作包括資料表中選取、 插入、 更新、 刪除和自訂陳述式<br/><br/>此連接器也包含 Microsoft 用戶端的 DRDA TCP/IP 網路連線到 Informix 伺服器。
[檔案](app-service-logic-connector-file.md) | 使用此連接器，您可以連線至內部部署檔案系統或網路，並完成不同的檔案工作，包括上傳、 刪除、 清單檔案，以及更多。
[Informix](app-service-logic-connector-informix.md) | 連接至 IBM Informix 資料庫，內部部署與執行 Windows 作業系統 Azure 虛擬機器上。 可以將 Web API 和 OData API 作業對應 Informix 結構化查詢語言命令。<br/><br/>沒有觸發程序。 動作包括資料表中選取、 插入、 更新、 刪除和自訂陳述式。<br/><br/>使用內部部署，VPN 或 Azure ExpressRoute 就能使用。 此連接器也包含 DRDA Informix 伺服器連線 TCP/IP 網路上的 Microsoft 客戶。
[Microsoft SQL Server](app-service-logic-connector-sql.md) | 連線至內部部署的 SQL Server 或 SQL Azure 資料庫。 您可以建立、 更新、 取得，並刪除 SQL 資料庫表格中的項目。
MQ | 連接至 IBM WebSphere MQ 伺服器版本 8，內部部署與執行 Windows 作業系統 Azure 虛擬機器上。 使用內部部署，VPN 或 Azure ExpressRoute 就能使用。 連接器也包含 MQ Microsoft 用戶端。<br/><br/>沒有觸發程序。 任何動作。<br/><br/>**附註**目前無法使用邏輯應用程式使用。

## <a name="connectors-as-triggers"></a>為觸發程序的連接器
多個連接器的邏輯應用程式提供引動程序。 這些引動程序有兩種類型︰

1. 投票引動程序︰ 這些引動程序獲得您指定的頻率，檢查新資料的服務。 當有新的資料時，新的執行個體邏輯應用程式執行的資料輸入。 若要防止所使用的相同資料多次，觸發程序可能會清除讀取與傳送到邏輯應用程式的資料。 這類連接器的範例是檔案、 SQL 和 Azure 儲存體。
2. 推入引動程序︰ 這些引動程序接聽的端點上的資料，或發生的事件。 然後，觸發邏輯應用程式的新的執行個體。 這類連接器的範例是 HTTP 接聽和 Twitter。

## <a name="connectors-as-actions"></a>為動作的連接器
連接器也可以用為邏輯應用程式中的動作。 動作可用於尋找邏輯的應用程式可用於執行內的資料。 例如，您可能需要查閱資料從 SQL 資料庫，如需客戶時處理順序。 或者，您可能需要撰寫、 更新或刪除資料的目的地。 您可以使用連接線所提供的動作。 （如下所定義其 Swagger 中繼資料），動作會對應至 API 應用程式中的作業。

## <a name="create-your-own-connectors-and-api-apps"></a>建立您自己的連接器和 API 應用程式
[連接器與 API 應用程式參照](http://aka.ms/appservicesconnectorreference)  
[Azure 應用程式服務 API 應用程式引動程序](../app-service-api/app-service-api-dotnet-triggers.md)  
[邏輯應用程式參考](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>需連接器與 API 應用程式
[什麼是連接器和 BizTalk API 應用程式](app-service-logic-what-are-biztalk-api-apps.md)  
[Azure 應用程式服務中使用混合式連線管理員](app-service-logic-hybrid-connection-manager.md)  
[管理及監視內建 API 應用程式與連接器](app-service-logic-monitor-your-connectors.md)
