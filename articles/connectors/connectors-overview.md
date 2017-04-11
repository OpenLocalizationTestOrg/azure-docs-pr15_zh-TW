<properties
    pageTitle="邏輯應用程式連接器概觀 |Microsoft Azure"
    description="可在邏輯應用程式的連接器的概觀"
    services=""
    documentationCenter="" 
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>使用邏輯應用程式中的連接器

連接器提供服務的通訊協定，與平台快速存取事件、 資料及動作。  邏輯應用程式支援的連接器的完整清單可以[在這裡找到](apis-list.md)。  連接器可以使用觸發程序或邏輯應用程式中的動作，而且可能會需要設定的*連線*以使用 (例如︰ 授權存取或代表您張貼的 Twitter 帳戶)。

## <a name="basics"></a>基本概念

連接器是裝載的服務，您可以存取要與其他服務，例如 Dynamics Azure Salesforce，[及更多](apis-list.md)整合邏輯應用程式的一部分。  他們的部署及管理 microsoft，讓您可以建立使用小數位數、 處理量及安全性處理整合工作流程。  您可以新增邏輯應用程式的連接器，藉由搜尋並選取連接器動作或底下**顯示 Microsoft 受管理的 Api**的觸發程序。

![選取 [觸發程序的 [動作] 功能表][1]

每個連接器動作 」 或 「 觸發程序有及其以設定屬性設定。  您可以按一下 [資訊] 按鈕，若要進一步瞭解巨集指令，或其文件[，瞭解更多](apis-list.md)參照。

如果您想要的服務或不還連接器，您也可以延伸邏輯應用程式[的自訂連接器](../app-service-logic/app-service-logic-create-api-app.md)或只撥直接服務等 HTTP 通訊協定的 API 整合。

## <a name="triggers"></a>觸發程序

某些連接器有觸發程序，這表示該連接器的事件會引發邏輯應用程式和傳入的任何資料，做為觸發程序的一部分。  觸發程序一律是邏輯應用程式中的第一個步驟。  常用的觸發程序包括等作業︰
 
 * 循環-執行每小時
 * 當 HTTP 收到要求
 * 當項目會新增至佇列中
 * 當收到電子郵件
 
某些引動程序會啟動的立即透過邏輯應用程式時，通知發生的事件，其他人必須設定頻率邏輯應用程式會檢查服務事件 （最多每 15 秒） 循環間隔。  

後收到事件時，執行的邏輯應用程式會啟動，並將啟動工作流程中的動作。  您也能夠存取整個 （例如 '開啟新的推文中 「 觸發程序會將貼入 tweet 傳遞到執行） 工作流程的觸發程序的任何資料。

## <a name="actions"></a>動作

大部分的連接器有一或多個動作，可以執行工作流程的一部分。  動作會執行有引發觸發程序之後，會發生任何步驟。  若要新增動作按一下**新的步驟**] 按鈕和連接器搜尋您想要使用。  一旦選取 （和之後，可能需要任何[連線](#connections)的設定），您會看到您可以設定的巨集指令卡片。  您可以按一下任何輸出的權杖從先前的步驟中選取資料，或視需要輸入的任何其他設定。

![設定連接器的巨集指令][2]

## <a name="connections"></a>連線

大部分的連接器會要求您設定的*連線*，您才能使用連接器。  若要存取連接器所需的任何登入或連線設定*連線*。  使用 OAuth，建立連接的連接器的表示登入服務 （例如 Office 365、 Salesforce 或 GitHub） 可以加密並安全地儲存 Azure 私人存放您存取權杖。  其他連接器 （例如 FTP:// 或 SQL） 需要含有與伺服器位址、 使用者名稱和密碼設定連線。  也加密和安全地儲存這些連線設定詳細資料。  連線無法存取服務，只要服務可讓。  Azure Active Directory OAuth 連線 （例如 Office 365 或動態） 我們可以繼續無限期重新整理存取權杖。  其他服務可能會在多久我們可以使用權杖，不會更新該放置限制。  在 [一般某些動作，例如變更密碼會使所有存取權杖。  

可以檢視和管理 Azure 中，按一下 [**瀏覽**並選取**API 連線**的連線。  從 API 連線資源可以檢視、 編輯、 更新或重新授權任何已建立的連線。

## <a name="next-steps"></a>後續步驟

- [建立您的第一個邏輯應用程式](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [瞭解常見用途與邏輯應用程式的範例](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [開始使用企業整合引動程序及動作](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png