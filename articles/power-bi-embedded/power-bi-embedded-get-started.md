<properties
   pageTitle="開始使用 Microsoft Power BI 內嵌"
   description="Power BI 內嵌、 將您的商務智慧應用程式的互動式 Power BI 報表"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>開始使用 Microsoft Power BI 內嵌

**Power BI 內嵌**是 Azure 服務，讓應用程式開發人員互動式 Power BI 報表將自己的應用程式。 **Power BI 內嵌**搭配現有的應用程式，並不需要重新設計，或變更的方式使用者登入。

透過[Azure ARM Api](https://msdn.microsoft.com/library/mt712306.aspx)佈建**Microsoft Power BI 內嵌**的資源。 在此情況下，您佈建的資源是**Power BI 工作區集合**。

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>建立工作區集合
在**工作區集合**是最上層的 Azure 資源與容器，將會內嵌在您的應用程式的內容。 兩種方式可以建立**工作區集合**︰

   -    使用手動 Azure 入口網站
   -    使用以程式設計方式 Azure 資源 Manager(ARM) Api

現在就讓我們逐步執行的步驟來建立**工作區集合**使用 Azure 入口網站。

   1.   開啟並登入**Azure 入口網站**︰ [http://portal.azure.com](http://portal.azure.com)。

   2.   按一下 [ **+ 新增**上方面板]。

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   在**資料 + 分析**中，按一下 [**內嵌 Power BI**]。
   4.   在**建立刀**中，輸入所需的資訊。 **價格**，請參閱[Power BI 內嵌價格](http://go.microsoft.com/fwlink/?LinkID=760527)。

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. 按一下 [**建立**]。

**工作區集合**會需要一些時間進行佈建。 完成時，您會進入**工作區集合刀**。

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

**建立刀**包含您要撥打的 Api 建立工作區，並將內容部署的資訊。

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>檢視 Power BI API 便捷鍵

其中一個最重要的組件的通話 Power BI REST Api 所需是資訊的**便捷鍵**。 這些用來產生**應用程式權杖**，用來驗證您的 API 邀請。 若要檢視您的**便捷鍵**，按一下 [**設定刀****便捷鍵**。 更多相關**應用程式權杖**，請參閱[Authenticating 及使用內嵌 Power BI 授權](power-bi-embedded-app-token-flow.md)。

   ![](media\power-bi-embedded-get-started\access-keys.png)

您將 「 您有兩個索引鍵的通知。

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

複製這些按鍵，並將其安全地儲存在您的應用程式。 請務必讓您將這些按鍵即密碼，因為它們會提供您的**工作區集合**中的所有內容的存取權。

列出兩個索引鍵，同時在特定時間需要一個索引鍵。 第二個機提供讓您可以不會中斷服務的存取定期重新產生金鑰。

您已經有的 Power BI 應用程式，及**便捷鍵**執行個體，您可以將您自己的應用程式匯入報表。 瞭解如何匯入報表之前下, 一節說明建立 Power BI 資料集和內嵌至應用程式的報表。

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>建立 Power BI 資料集和內嵌至應用程式的報表

現在您已經建立執行個體的 Power BI 應用程式並**便捷鍵**，您會需要建立 Power BI 資料集和您想要內嵌的報表。 可以使用**Power BI Desktop**建立資料集和報表。 您可以下載[免費的 Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)。 或者，若要快速開始，您可以下載 [零售分析範例 PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547)。 若要瞭解如何使用**Power BI Desktop**詳細資訊，請參閱[Power BI Desktop 快速入門](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop)。

有了**Power BI Desktop**，您連線至您的資料來源將**Power BI Desktop**匯入資料的複本，或直接連接到使用**不同**的資料來源。

以下是使用**匯入**和**DirectQuery**之間的差異。

|匯入 | 不同
|---|---
|匯入或複製到**Power BI Desktop**表格資料行*和資料*。 當您使用視覺效果， **Power BI Desktop**查詢資料的複本。 若要查看基礎資料的任何變更，您必須重新整理]，或匯入，請完成、 目前資料集一次。|*資料表和資料行*，僅匯入或複製到**Power BI Desktop**。 當您使用視覺效果， **Power BI Desktop**查詢基礎資料來源，這表示您永遠檢視目前的資料。

詳細資料來源連線的相關資訊，請參閱[連線至資料來源](power-bi-embedded-connect-datasource.md)。

在**Power BI Desktop**中儲存工作後，會建立 PBIX 檔案。 這個檔案包含您的報表。 此外，如果您匯入資料 PBIX 包含完整的資料集，，或 PBIX 如果您使用**不同**，包含只資料集的結構描述。 以程式設計方式將 PBIX 部署到使用[Power BI 匯入 API](https://msdn.microsoft.com/library/mt711504.aspx)工作區。

> [AZURE.NOTE] **Power BI 內嵌**有其他的 Api，若要變更的伺服器和指向您的資料集的資料庫，並設定服務帳戶認證集將用來連線到您的資料庫。 請參閱[文章 SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx)及[更新閘道器的資料來源](https://msdn.microsoft.com/library/mt711498.aspx)。

## <a name="next-steps"></a>後續步驟
在先前的步驟，您可以建立工作區集合第一份報表和資料集。 現在正是若要瞭解如何針對**Power BI 內嵌**撰寫程式碼。 若要協助您開始使用，我們所建立的範例 web 應用程式︰[快速入門的範例](power-bi-embedded-get-started-sample.md)。 範例會示範如何以︰

  - 佈建內容
      - 建立工作區
      - 匯入 PBIX 檔案
      - 更新連線字串，並設定您的資料集的認證]。

  - 安全地內嵌報表

## <a name="see-also"></a>另請參閱
- [開始使用範例](power-bi-embedded-get-started-sample.md)
- [驗證並使用 Power BI 內嵌授權](power-bi-embedded-app-token-flow.md)
- [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
