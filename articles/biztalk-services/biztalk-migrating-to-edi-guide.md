<properties   
    pageTitle="移轉 BizTalk Server EDI 解決方案 BizTalk Services 技術指南 |Microsoft Azure"
    description="將 EDI 移轉到 MABS;Microsoft Azure BizTalk 服務"
    services="biztalk-services"
    documentationCenter="na"
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags 
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>BizTalk 服務移轉 BizTalk Server EDI 解決方案︰ 技術指南

作者︰ Tim Wieman 和 Nitin Mehrotra

檢閱者︰ Karthik Bharthy

使用撰寫︰ Microsoft Azure BizTalk 服務-2014 年 2 月發行。

## <a name="introduction"></a>簡介

電子資料交換 (EDI) 是一種最常見的方法，企業的 exchange 資料以電子方式，也在名為企業對企業或 B2B 交易。 BizTalk 伺服器已經 EDI 支援透過十，自初始 BizTalk 伺服器版本。 使用 BizTalk 服務 Microsoft 會繼續 EDI 解決方案的支援 Microsoft Azure 平台上。 B2B 交易大多外部組織中，因此如果雲端的平台上實作實作變得更容易。 Microsoft Azure 提供此功能，透過 BizTalk 服務。

有些客戶查看新 EDI 解決方案的 「 greenfield 」 平台的 BizTalk 服務，許多客戶有他們可能會想要移轉至 Azure 的目前 BizTalk 伺服器 EDI 解決方案。 因為架構 BizTalk 服務 EDI 是根據 BizTalk 伺服器 EDI 架構 （交易合作夥伴、 實體合約） 為相同重要的項目可能是 BizTalk 服務移轉 BizTalk 伺服器 EDI 成品。

這份文件討論 BizTalk 服務移轉 BizTalk 伺服器 EDI 成品的相關的差異。 這份文件假設 BizTalk 伺服器 EDI 處理和交易合作夥伴合約知識。 如需有關 BizTalk 伺服器 EDI 的詳細資訊，請參閱[交易合作夥伴管理使用 BizTalk 伺服器](https://msdn.microsoft.com/library/bb259970.aspx)。

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>哪個版本的 BizTalk 伺服器 EDI 成品可以也會移轉給 Biztalk？

大幅 BizTalk Server 2010，增強型 BizTalk 伺服器 EDI 模組，它重塑合作夥伴、 設定檔，及合約時。 Biztalk 組織交易夥伴和商務細分這些交易夥伴中的使用相同的模型。 如此一來，從 BizTalk Server 2010 及更新版本中升級 EDI 成品 BizTalk 服務中，是很多直接轉寄的程序。 若要移轉 EDI 成品聯 BizTalk Server 2010 之前的版本，您必須先 BizTalk Server 2010 升級，然後將您 EDI 成品移轉到 BizTalk 服務。

## <a name="scenariosmessage-flow"></a>案例 [郵件流程

為 BizTalk server EDI BizTalk 服務中處理專題建立交易夥伴管理 (TPM) 解決方案。 TPM 方案包含下列主要元件︰

- 商業夥伴，代表組織 B2B 交易中。
- 設定檔，代表細分內交易的夥伴。
- 交易合作夥伴合約 （或合約），代表商務協議之間兩個合作夥伴/設定檔。

下圖說明相似，以及 BizTalk 伺服器 EDI 解決方案與 BizTalk 服務 EDI 方案之間的差異︰

![][EDImessageflow]

主要的差異及 EDI 解決方案流程 BizTalk server 和 BizTalk 服務之間的相似處，如下︰

- 就像 BizTalk Server 會使用 EDIReceive 管線接收 EDI 訊息和 EDISend 管線傳送 EDI 訊息，BizTalk 服務會使用 EDI 接收橋接收及傳送 EDI 橋 EDI 郵件傳送。 BizTalk Server 中的管線與協議相關聯藉由傳送或接收連接埠。 在 BizTalk 服務合約本身表示傳送或接收橋]。
- 在 BizTalk Server EDIReceive 管線處理 EDI 的訊息之後, 郵件傾印到 SQL Server 資料庫。 EdiSend 管線然後挑選 [從 SQL Server 資料庫郵件、 處理，然後至交易夥伴傳送。

    在 BizTalk 服務 EDI 收到之後橋處理 EDI 的訊息中，它將郵件路由到外部的程序。 Microsoft Azure 或內部部署上無法執行外部的程序。 外部的程序應該將郵件路由至 EDI 傳送橋;傳送橋原本就不接收郵件。 處理郵件之後, EDI 傳送橋會將郵件路由到交易夥伴。

Biztalk 提供容易使用設定經驗來快速建立並部署 B2B 合約商業夥伴不設定任何 Microsoft Azure 計算執行個體 （網頁或背景工作角色）、 任何 Microsoft Azure SQL 資料庫] 或任何 Microsoft Azure 儲存體帳戶之間。 更複雜的案例會要求將工作流程或其他服務處理中 」 邊緣 」 交易合作夥伴合約，也就是之前或之後交易合作夥伴合約 EDI 橋處理。 在 [詳細資料，請處理 BizTalk 服務中的 EDI 訊息期間會發生以下的事件順序。

1. 從交易合作夥伴 Fabrikam 收到 EDI 訊息。  如需 EDI 從接收郵件交易的合作夥伴，Biztalk 支援傳輸通訊協定，例如 FTP、 SFTP、 AS2 和 HTTP/s 鍵。

2. 交易的合作夥伴合約接收端處理分解 EDI 郵件 XML 格式。  您可以將反組譯的 EDI 中的郵件 （XML 格式） 路由傳送至服務匯流排轉送端點、 服務匯流排主題、 服務匯流排佇列中或 Biztalk 橋等服務匯流排結束點。

3. 反組譯的 XML 郵件可能會自進一步自訂處理的端點。  無法藉由內部部署元件或 Microsoft Azure 計算的執行個體，以進一步處理程序中的 Windows 工作流程 (WF) 或 Windows Communication Foundation (WCF) 服務，例如處理這些端點。

4. 「 傳送端處理 」 的交易夥伴合約然後會組合成 EDI 格式的 XML 訊息，並傳送給交易夥伴，Contoso。  交易的合作夥伴，才能傳送 EDI 訊息，BizTalk 服務可支援相同的通訊協定，如收到 EDI 訊息。

這份文件進一步提供概念性指引在移轉部分不同 BizTalk 伺服器 EDI 成品 BizTalk 服務。

## <a name="sendreceive-ports-to-trading-partners"></a>傳送/接收連接埠至商業夥伴

BizTalk Server 中您設定接收位置和接收連接埠 EDI/XML 郵件接收交易的合作夥伴，且您設定傳送 EDI/XML 郵件給交易夥伴傳送連接埠。 您將使用 BizTalk 伺服器管理主控台交易合作夥伴合約下列連接埠。 在 BizTalk 服務中，您收到訊息的商業夥伴的位置和您傳送郵件商業夥伴設定交易合作夥伴合約本身 （作為傳輸設定的一部分） BizTalk Services 入口網站中的一部分。  因此您真的沒有的 「 傳送連接埠 」 和 「 接收位置 」 的概念，BizTalk 服務中。 如需詳細資訊，請參閱[建立合約](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx)。

## <a name="pipelines-bridges"></a>管線 （橋）

在 BizTalk 伺服器 EDI，管線是郵件處理實體，也可以包含特定處理功能，所需的應用程式的自訂邏輯。 如需 BizTalk 服務，相當於想 EDI 橋。 不過在 BizTalk 服務，現在，EDI 橋是 「 關閉 」。  也就是說，您無法將您自己的自訂活動加入 EDI 橋。 任何自訂的程序必須完成外 EDI 橋應用程式中之前, 或之後的郵件進入橋為交易合作夥伴合約的一部分。 EAI 橋有執行自訂處理] 選項。 如果您想要自訂的處理，您可以使用 EAI 橋之前或之後 EDI 橋處理郵件。 如需詳細資訊，請參閱[如何橋中包含自訂程式碼](https://msdn.microsoft.com/library/azure/dn232389.aspx)。

您可以插入發佈/訂閱的流程使用自訂程式碼及/或使用服務匯流排訊息佇列 」 和 「 主題交易的合作夥伴合約會收到訊息之前, 或之後合約處理郵件，並將其傳送到服務匯流排結束點。

郵件流程圖樣本主題中，請參閱**案例 [郵件流程**。

## <a name="agreements"></a>合約

如果您熟悉 BizTalk Server 2010 貿易合作夥伴合約用於 EDI 程序，然後 Biztalk 交易合作夥伴合約不陌生。 大部分的合約設定的相同，並使用相同的術語。 在某些情況下，合約設定會更容易比較 BizTalk 伺服器中相同的設定。 Microsoft Azure Biztalk 支援 X12 EDIFACT，與 AS2 傳輸。

Microsoft Azure Biztalk 也會提供**TPM 資料移轉**工具將交易夥伴與協議從 BizTalk 伺服器交易合作夥伴模組移轉到 BizTalk Services 入口網站。 TPM 資料移轉工具有工具套件的一部分，其中可以從下載[MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057)。 封裝也包含說明如何使用之工具的工具，而且基本的疑難排解資訊讀我檔案。

## <a name="schemas"></a>結構描述

Biztalk 提供 EDI 結構描述，可在 BizTalk 服務方案。  此外，BizTalk 伺服器 EDI 結構描述也可使用 BizTalk 服務因為跨 BizTalk 伺服器，以及 Biztalk EDI 結構描述的根節點相同。 因此，您將無法直接 BizTalk 伺服器 EDI 結構描述並在要開發使用 BizTalk 服務 EDI 解決方案中使用它們。 您也可以從[MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057)下載結構描述。

## <a name="maps-transforms"></a>地圖 （轉換）

BizTalk Server 中的地圖 BizTalk 服務中稱為 「 轉換。 從 BizTalk Server 移轉的地圖 BizTalk 服務可能更複雜的工作，以獲得 （根據地圖複雜度）。 用於 Biztalk 對應工具是不同的 BizTalk 對應項目。 雖然的對應程式大多呈現相同外觀，基礎地圖格式是不同的。 使用者可以使用運算質 （稱為中的**地圖作業**BizTalk 服務），也會不同。  實際上，您無法在 Biztalk 直接使用 BizTalk 地圖。 此外，用於 BizTalk Server 並非所有運算質可為 BizTalk 服務中的地圖作業。

### <a name="new-transform-operations"></a>新的轉換作業

轉換地圖作業可使用清單看起來似乎很不同 BizTalk Server 對應表時, BizTalk 服務轉換有新的完成相同的工作的方法。 例如，BizTalk 服務轉換有提供的**清單作業**。 不提供 BizTalk 對應。  **清單作業**可讓您建立和操作 「 清單 」，清單是一組的項目 （又稱為 「 資料列 」），而且每個項目可以有多個成員 （也稱為 「 欄 」）。  您可以排序清單，選取 [根據條件等項目]。

另一個範例中 BizTalk 服務轉換的新功能是**循環播放作業**。  很難 BizTalk Server 對應程式中建立的巢狀迴圈。  因此，循環圖作業被新增 BizTalk 服務轉換。

還有另一個範例是**If If-then-else**運算式地圖作業。  執行 if if-then-else 作業可能在 BizTalk 對應工具，但它所需的多個運算質完成像是簡單的工作。

### <a name="migrating-biztalk-server-maps"></a>移轉 BizTalk Server 地圖

Microsoft Azure Biztalk 提供的工具來移轉 BizTalk Server 對應至 Biztalk 轉換。 **BTMMigrationTool**有[BizTalk 服務 SDK 下載](http://go.microsoft.com/fwlink/p/?LinkId=235057)所提供的**工具**套件的一部分。 如需有關工具的詳細資訊，請參閱[轉換 BizTalk 服務轉換 BizTalk 地圖](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx)。

您也可以查看如何[移轉 Biztalk 轉換 BizTalk Server 地圖](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx)Sandro Pereira，BizTalk MVP 的範例。

## <a name="orchestrations"></a>協調流程

如果您需要移轉到 Microsoft Azure 處理 BizTalk Server 協調流程，因為 Microsoft Azure 不支援執行 BizTalk Server 協調流程重新撰寫需要協調流程。  您可以重寫 Windows 工作流程 Foundation 4.0 (WF4) 服務中的協調流程功能。  這是整個重寫目前沒有從移轉 BizTalk Server 協調流程到 WF4 現狀。 以下是一些 Windows 工作流程的資源︰

- [*如何將整合服務匯流排佇列與主題 WCF 工作流程服務*](https://msdn.microsoft.com/library/azure/hh709041.aspx)來 Paolo Salvatori。 

- 從建置 2011年會議[*建置應用程式，搭配 Windows Workflow Foundation 和 Azure*工作階段](http://go.microsoft.com/fwlink/p/?LinkId=237314)。

- MSDN 上的[*Windows 工作流程 Foundation 開發人員中心*](http://go.microsoft.com/fwlink/p/?LinkId=237315)。

- MSDN 上的[*Windows 工作流程 Foundation 4 (WF4) 的文件*](https://msdn.microsoft.com/library/dd489441.aspx)。

## <a name="other-considerations"></a>其他考量

以下是一些您必須先使用 BizTalk 服務時的考量。

### <a name="fallback-agreements"></a>後援合約

BizTalk 伺服器 EDI 處理具有 「 後援合約 」 的概念。  BizTalk 服務並**不**到目前為止已後援合約概念。  後援合約 BizTalk Server 中的使用方式，請參閱 BizTalk 文件主題[合約角色 EDI 處理的方式](http://go.microsoft.com/fwlink/p/?LinkId=237317)和[通用設定] 或 [後援合約內容](https://msdn.microsoft.com/library/bb245981.aspx)的資訊。

### <a name="routing-to-multiple-destinations"></a>傳遞到多個目的地

Biztalk 橋目前狀態不支援將訊息傳送至多個目的地使用發行-訂閱模型。 請改為您無法傳送郵件從 Biztalk 橋服務匯流排主題，然後可能有多個訂閱，若要在多個端點收到訊息。

## <a name="conclusion"></a>結束時

Microsoft Azure BizTalk 服務會在一般里程碑，以新增更多的功能及功能更新。 每次更新，我們支援增強的功能，以促進建立使用 Biztalk 和其他 Azure 技術的端對端解決方案。

## <a name="see-also"></a>另請參閱

[開發 Azure 與企業應用程式](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
