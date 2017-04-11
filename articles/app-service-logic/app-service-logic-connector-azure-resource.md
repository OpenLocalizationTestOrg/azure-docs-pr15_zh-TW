<properties
   pageTitle="使用邏輯應用程式中的 Azure 資源連接器 |Microsoft Azure 應用程式服務"
   description="如何建立及設定 Azure 資源連接器或 API 應用程式，並將其用於 Azure 應用程式服務中的邏輯應用程式"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>開始使用 Azure 資源連接器並將其新增至您的邏輯應用程式
>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽架構版本。

您可以使用 Azure 資源連接器，輕鬆地管理邏輯應用程式內的 [Azure 資源。

## <a name="create-the-azure-resource-connector"></a>建立資源 Azure 連接器
若要使用的 Azure 資源連接器 API 應用程式，您需要先建立的執行個體。 這可以完成任一直接建立邏輯應用程式時，或選取 [從 Azure Marketplace 的 [Azure 資源管理員連接器 API 應用程式。

若要設定，必須您需要設定設定服務主要具備執行上述程序是您想要做 Azure 中的權限。 所有的通話會進行代表的此服務主要的設定。 這個選項可讓您設定範圍連接器，以使用只完全與您想要其待辦事項]，而無其他。

David Ebbo 具有如何設定在撰寫[好的部落格文章](http://blog.davidebbo.com/2014/12/azure-service-principal.html)。 請遵循所有此處的指示，並取得您的**租用戶識別碼**、**用戶端識別碼**和**密碼**。 這三個欄位，以及**訂閱 ID**是什麼，才能設定連接器。

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>使用邏輯應用程式的設計工具中的 Azure 資源連接器
### <a name="trigger"></a>觸發程序
有兩個引動程序支援的連接器︰

名稱 | 描述
---- | -----------
事件發生時 | 您的訂閱中的事件發生時，觸發程序。
公制交叉閥值 |  當度量單位，符合特定閾值時，觸發程序。

### <a name="action"></a>巨集指令

同樣地，您可以提供大量動作內 Azure 訂閱︰

**資源**群組中，您可以︰

名稱 | 描述
---- | -----------
列出資源群組 | 列出所有訂閱中的資源群組。
取得資源群組 | 資源群組取得其識別碼。
建立資源群組 | 建立或更新的資源群組。
刪除資源群組 | 刪除資源群組。

**資源**，您可以︰

名稱 | 描述
---- | -----------
清單資源 | 列出不同類型的篩選您的訂閱中的資源。
取得資源 | 取得其資源識別碼單一資源
建立或更新資源 | 建立資源，或更新現有的資源。 您必須提供的所有屬性的資源。
資源巨集指令 |  資源執行任何動作。 您必須知道動作名稱] 和 [此動作會 （如果有的話） 的內容。
刪除資源 | 刪除資源。

**資源**提供者，您可以︰

名稱 | 描述
---- | -----------
清單資源提供者 | 列出所有訂閱中可用的資源提供者。

**資源群組**部署中，您可以︰

名稱 | 描述
---- | -----------
清單部署 | 列出所有資源群組中的部署。
取得部署 | 取得其識別碼範本部署。
建立部署 | 建立新的資源群組部署提供範本。

**事件**的相關資源，您可以︰

名稱 | 描述
---- | -----------
取得事件 | 在 [訂閱或資源的成本，請取得事件。

有關資源的**度量**，您可以︰

名稱 | 描述
---- | -----------
取得指標 | 度量單位，取得資源識別碼。

## <a name="do-more-with-your-connector"></a>執行更多您的連接器
現在，建立連接器時，您可以將其新增至商務流程使用邏輯的應用程式。 請參閱[邏輯應用程式有哪些？](app-service-logic-what-are-logic-apps.md)。

>[AZURE.NOTE] 如果您想要開始使用 Azure 邏輯應用程式註冊 Azure 帳戶之前，請移至[試邏輯應用程式](https://tryappservice.azure.com/?appservice=logic)，可以讓您立即建立短暫入門邏輯應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

檢視在 Swagger REST API 參照[連接器與 API 應用程式參考](http://go.microsoft.com/fwlink/p/?LinkId=529766)。

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md
