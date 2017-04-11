<properties 
   pageTitle="Azure 應用程式服務中建立 B2B 程序 |Microsoft Azure" 
   description="如何建立商務商務程序概觀" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>建立 B2B 程序

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>商務案例 
Contoso 和北風是兩個業務合作夥伴。 Contoso （經銷商） 會透過產業層級傳輸，例如 AS2 傳送給 Northwind （供應商） 的採購單。 北風會將所有內送的訂單儲存其雲端儲存空間。 採購單是下列兩個合作夥伴之間的 XML 訊息。 當郵件儲存在 Northwind 的雲端儲存空間 Northwind 內部程序會處理該點的順序上。
 
本教學課程中的目標是建立 [北風可以如何建立商務程序透過其中可以 AS2 （購買 XML 中的訂單） 的郵件接收其合作夥伴 Contoso 並保存在其雲端儲存空間。


## <a name="capabilities-demonstrated"></a>示範的功能 
本教學課程中，可協助展現下列功能︰ 

- **郵件交通**︰ 零售商和供應商可能會不同的平台上，但是他們仍可達到兩者之間的通訊。 在本教學課程進行通訊，但在 AS2 (是否適陳述式 2)。 AS2 是傳輸資料之間的企業對企業溝通貿易協力廠商的常用方式。
- **資料保存**︰ 之後已收到訊息透過 AS2 然後北風想要保存之前進一步處理。 它可以使用 [連接器保存在雲端儲存區中的郵件。 在本教學課程 Azure Blob 所負責為雲端儲存空間北風。
- **建立商務程序**︰ 的流程，多個 API 應用程式可以裝訂放在一起以達到商務結果，如下所示。


## <a name="before-you-begin"></a>開始之前
本教學課程假設您有 Azure 應用程式服務的基本知識，瞭解如何建立 API 應用程式，並鏡頭組合在同一個流程。


## <a name="steps-to-achieve-the-business-scenario"></a>步驟來完成商務案例
**建立及設定的必要的 API 應用程式**

1. 建立**Azure Blob 儲存體的連接器**的執行個體。 需要 Azure 儲存體帳戶認證。 請確定已準備開始建立此之前。
2. 建立**BizTalk 交易夥伴管理**的執行個體。 需要空白 SQL 資料庫函數。 請確定已準備開始建立此之前。
3. 建立**AS2 連接器**的執行個體。 這也需要空白 SQL 資料庫函數。 請確定已準備開始建立此之前。 此外，如果您想要封存的郵件做為處理 AS2 的一部分，您可能會提供認證 Azure Blob 在其建立。
4. 設定所建立的 TPM （交易合作夥伴管理） 服務︰  
    1. 瀏覽至在上述步驟建立 TPM 服務的執行個體。
    2. 使用*元件*至 [**新增**新的合作夥伴為**「 contoso 」**和其設定檔] 下的 [**合作夥伴**] 選項新增必要的 AS2 身分識別。
    3. 使用*元件*至 [**新增**新的合作夥伴，名為 [**北風**和其設定檔] 下的 [**合作夥伴**] 選項新增必要的 AS2 身分識別。
    4. 使用北風和 Contoso 之間**新增**新的 AS2 合約*元件*] 下的**合約**選項。 北風會裝載的合作夥伴，而且 Contoso 便可來賓合作夥伴。 視需要在此合約建立期間設定簽章、 加密、 壓縮和通知。 使用需要憑證，他們可以上傳透過 [**憑證**] 選項時瀏覽 TPM 服務所建立。


## <a name="create-a-flow--business-process"></a>建立流程 / 商務程序
1. 建立新的流程中的第一個步驟是 AS2。 拖曳放**AS2 連接器**並選擇 [已建立的執行個體。 選擇 [觸發程序為功能︰  
    ![][1]  
2. 下一步拖曳放**Azure Blob 儲存體的連接器**並選擇已建立的執行個體。 選擇 [巨集指令的功能，並在其中，選取 [**上傳 Blob**為所需的功能。 設定視。
3. 現在建立/部署流程。


## <a name="message-processing--troubleshooting"></a>郵件處理與疑難排解
1. 就測試我們已部署的流程。 傳送 XML 訊息 （依照 AS2 合約先前所建立） 包裝中 AS2 AS2 端點運用 AS2Connector 執行個體所建立的。 您可能需要設定端點驗證，使其位於公開存取。
2. 執行資訊流程被運用瀏覽到流程，然後逐步執行您執行的流程執行個體
3. AS2 處理資訊，瀏覽至，AS2Connector 執行個體，然後依照逐步追蹤組件。 您可以使用時所需要的資訊限制檢視的篩選。

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 
