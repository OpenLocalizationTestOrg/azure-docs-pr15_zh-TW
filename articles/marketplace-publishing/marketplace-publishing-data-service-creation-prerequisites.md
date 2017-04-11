<properties
   pageTitle="建立資料服務商場的必要條件技術 |Microsoft Azure"
   description="瞭解建立部署和銷售 Azure marketplace 資料服務的需求"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>建立資料服務的必要條件技術提供的 Azure Marketplace

>[AZURE.IMPORTANT] **現在我們已不再登入任何新的資料服務的發行者。新的 dataservices 非取得已核准的清單。** 如果您有您想要發佈上 AppSource SaaS 商務應用程式，您就可以找到更多資訊[以下](https://appsource.microsoft.com/partners)。 如果您有 IaaS 應用程式，或您想要發佈 Azure 服務商場的開發人員服務可以找到更多資訊[以下](https://azure.microsoft.com/marketplace/programs/certified/)。

閱讀前開頭的程序並瞭解位置及原因執行每一個步驟。 盡可能，您應準備您的公司資訊及其他資料，下載所需的工具，及/或開頭優惠建立程序之前，先建立技術的元件。

您應該準備好開始此程序之前的下列項目︰

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>決定哪些技術將用來發佈您的資料服務優惠

Publisher 中 Azure Marketplace 發佈資料服務時的多個技術之間決定。 主要技術支援，如下所述。 使用者技術用來發佈資料服務，無論使用透過**OData 摘要**公開 Azure 服務商場的資料。 您可以找到 OData 服務的完整資訊[http://www.odata.org/](http://www.odata.org/)上

## <a name="sql-azure-database"></a>SQL Azure 資料庫

準備好 SQL Azure 中的資料集是發行者的責任。 您需要訂閱 Azure 佈建資料庫的適當的大小上, 傳至 SQL Azure 資料庫的資料。 發行者負責也讓自己的資料永遠保持最新狀態。 您可以找到[https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)上的訂閱 Azure 服務的詳細資訊


將資料移到 SQL Azure 時，Azure Marketplace 可以公開的資料表及檢視。 Publisher 可以指定哪些資料表/檢視和資料行公開給使用者。 進一步內容提供者也可以指定可讓使用者查詢的資料行，並僅傳回哪些內容中。 這會提供高層級的彈性的應公開資料庫中的資料。 可查詢的資料欄必須要備份的一或多個資料庫索引。

## <a name="rest-based-web-service"></a>根據其他 web 服務

支援的通訊協定︰**僅 HTTPS**

現有的基礎的其餘服務可以透過 Azure Marketplace 公開。 永遠為 OData 摘要使用者至公開資料集，因為 Azure Marketplace 服務需求能夠對應到 OData 的服務會依據服務。 若要這樣做其他基礎端點需要公開為 HTTP 參數的所有參數。

內容必須對應到 ATOM 回應表單中。 因此服務需求到 XML 格式，只可以從回應包含一個重複的項目，包含內容 （例如記錄設定）。 Azure 服務商場服務會對應到項目中的節點 ATOM 及裝載值重複節點，將屬性中的項目節點的節點。

授權資訊 （例如 API 金鑰，請驗證權杖，等） 必須提供為 HTTP 參數，或在 HTTP 標頭 （關鍵值組） – 也支援基本驗證。 需要提供正確的金鑰，透過 Azure Marketplace 的所有邀請會都完成該金鑰。 監控和帳單寄送的使用者，這時 Azure Marketplace 層級。

服務所傳回的錯誤，就必須對應到 HTTP 狀態碼。 萬一服務會傳回包含錯誤的 XML 這些要對應 HTTP 狀態碼 Azure Marketplace 服務。

## <a name="soap-based-web-services"></a>根據 SOAP web 服務

通訊協定︰**僅 HTTPS**

需求是與其他相同基礎的服務] 區段中。 唯一的差別也可以在每個要求透過 Azure Marketplace 遞送發行者的服務 XML 內文中提供參數。 這表示使用者提供在前端 HTTP 參數成數 XML 文件張貼內容提供者的 web 服務要求的 XML 項目。

## <a name="odata-based-web-services"></a>OData 依據 web 服務

通訊協定︰**僅 HTTPS**

可以 Azure Marketplace OData 服務為公開資料。 系統會前往傳遞到服務，並以 Azure Marketplace 服務根目錄 – 以確保所有的後續通話可幫助 Azure Marketplace 取代服務的根目錄。

OData 服務不只需要到後端資料庫。 OData 支援的任何一種磁碟機服務的儲存空間或商務邏輯。


## <a name="next-steps"></a>後續步驟
現在，您檢閱的必要條件，完成的必要工作，您可以建立您的資料服務提議為詳細[資料服務發佈指南](marketplace-publishing-data-service-creation.md)中向前移動。

或者，如果您想要檢閱每個發佈階段的整體的程序與個別文件，請造訪，請參閱[快速入門︰ 如何將提供發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)。

[link-acct]:marketplace-publishing-accounts-creation-registration.md
