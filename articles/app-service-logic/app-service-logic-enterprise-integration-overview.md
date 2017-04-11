<properties 
    pageTitle="企業整合概觀 |Microsoft Azure 應用程式服務 |Microsoft Azure" 
    description="若要啟用商務程序 」 和 「 整合案例使用邏輯應用程式中使用企業整合的功能" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-the-enterprise-integration-pack"></a>企業整合套件的概觀

## <a name="what-is-the-enterprise-integration-pack"></a>什麼是企業整合套件？
企業整合套件是 Microsoft 的雲端解決方案完美啟用企業對企業 B2B 通訊。 套件使用業界標準通訊協定，包括[AS2](./app-service-logic-enterprise-integration-as2.md)、 [X12](./app-service-logic-enterprise-integration-x12.md)，以及[EDIFACT](./app-service-logic-enterprise-integration-edifact.md)交換商業夥伴之間的訊息。 可以使用加密與數位簽章也受到保護的郵件。 

套件可讓組織使用不同的通訊協定，藉由將兩個組織的系統可以解譯並採取行動的格式轉換不同的格式，以電子方式 exchange 郵件格式。 

如果您熟悉 BizTalk 伺服器或 Microsoft Azure BizTalk 服務，您會看到易於使用企業整合功能，因為大部分的概念類似。 一項主要差異，是企業整合使用整合帳戶以簡化的儲存空間及管理用於 B2B 通訊的成品。 

企業整合套件架構，根據 「**整合帳戶**儲存所有可用來設計、 部署及維護 B2B 應用程式的成品。 整合帳戶基本上是雲端的容器，您可以在此儲存例如結構描述、 合作夥伴、 憑證、 地圖及合約的成品。 若要建立 B2B 工作流程然後邏輯應用程式中使用這些成品。 您可以使用成品中的邏輯應用程式之前，必須先將整合客戶連結至您的邏輯應用程式。 之後將它們的連結，邏輯應用程式都可以存取整合帳戶的成品。  

## <a name="why-should-you-use-enterprise-integration"></a>為什麼應該使用企業整合？
- 企業整合，您就可以將您所有的成品儲存在一個位置，也就是您整合的帳戶。 
- 您可以利用邏輯應用程式引擎和其所有建立 B2B 工作流程，以及與第 3 廠商 SaaS 應用程式、 內部部署的應用程式，以及自訂的應用程式整合的連接器
- 您也可以利用 Azure 函數

## <a name="how-to-get-started-with-enterprise-integration"></a>如何開始使用企業整合？
您可以建立及管理 B2B 使用企業整合套件邏輯應用程式設計工具透過**Azure 入口網站**上的應用程式。  

您也可以使用[PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "邏輯應用程式的主題 PowerShell")來管理您的邏輯應用程式。 

以下是您需要採取 Azure 入口網站中建立應用程式之前的步驟概觀︰![概觀圖像](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>什麼是一些常見的案例？

企業整合支援這些業界標準︰   

- EDI-電子資料交換  
- EAI-企業應用程式整合  

## <a name="heres-what-you-need-to-get-started"></a>以下是您需要即可開始使用
- 整合帳戶 Azure 訂閱
- 若要建立地圖和結構描述的 visual Studio 2015
- [Microsoft Azure 邏輯應用程式企業整合 Tools for Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>試試看
[立即試用](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)部署完全營運樣本 AS2 傳送及接收] 使用邏輯應用程式的 B2B 功能的邏輯應用程式。

## <a name="learn-more-about"></a>深入瞭解︰
- [合約](./app-service-logic-enterprise-integration-agreements.md "瞭解企業整合合約")
- [企業 (B2B) 案例](./app-service-logic-enterprise-integration-b2b.md "瞭解如何建立使用 B2B 功能邏輯應用程式")  
- [憑證](./app-service-logic-enterprise-integration-certificates.md "瞭解企業整合憑證")
- [編碼/解碼一般檔案](./app-service-logic-enterprise-integration-flatfile.md "瞭解如何編碼和解碼一般檔案內容")  
- [整合帳戶](./app-service-logic-enterprise-integration-accounts.md "深入了解整合帳戶")
- [地圖](./app-service-logic-enterprise-integration-maps.md "瞭解企業整合地圖")
- [合作夥伴](./app-service-logic-enterprise-integration-partners.md "瞭解企業整合合作夥伴")
- [結構描述](./app-service-logic-enterprise-integration-schemas.md "瞭解企業整合的結構描述")
- [XML 訊息驗證](./app-service-logic-enterprise-integration-xml.md "瞭解如何將驗證邏輯應用程式使用 XML 訊息")
- [XML 轉換](./app-service-logic-enterprise-integration-transform.md "瞭解企業整合地圖")
- [企業整合連接器](../connectors/apis-list.md "瞭解企業整合套件連接器")



