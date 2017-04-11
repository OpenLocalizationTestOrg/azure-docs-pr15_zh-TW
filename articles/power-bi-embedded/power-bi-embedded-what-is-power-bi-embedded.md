<properties
   pageTitle="什麼是 Microsoft Power BI 內嵌？"
   description="Power BI 內嵌可讓您將 Power BI 報表整合到 web 或行動應用程式，因此您不需要建立自訂解決方案呈現您的使用者"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="what-is-microsoft-power-bi-embedded"></a>什麼是 Microsoft Power BI 內嵌？

使用**Power BI 內嵌**，您可以整合 Power BI 報表右將您的網頁或行動應用程式。

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI 內嵌是**Azure 服務**可讓您 Isv 及應用程式開發人員，以運用在其應用程式中的 Power BI 資料體驗。 開發人員]，您建立了應用程式，這些應用程式有自己的使用者及組不同的功能。 這些應用程式也可能會發生將類似的圖表和報表可以現在會由 Microsoft Power BI 內嵌的部分內建資料項目。 使用者不需要使用應用程式的 Power BI 帳戶。 他們可以繼續登入您的應用程式，就像之前，並檢視並與之互動 Power BI 報表體驗，而不需要任何額外的授權。

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Microsoft Power BI 授權內嵌

**Microsoft Power BI 內嵌**的使用狀況模型] 中，使用 Power BI 授權不一般使用者的責任。  不過，**呈現**所購買的視覺效果，使用應用程式開發人員，並向擁有這些資源的訂閱。

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI 內嵌概念模型

![](media\powerbi-embedded-whats-is\model.png)

Azure 中任何其他服務，例如資源的內嵌 Power BI 佈建透過[Azure ARM Api](https://msdn.microsoft.com/library/mt712306.aspx)。 在此情況下，您佈建資源是**Power BI 工作區集合**。

## <a name="workspace-collection"></a>工作區集合

**工作區集合**是最上層 Azure 容器的資源，包含 0 或更多**工作區**。  **工作區****集合**具有所有標準 Azure 屬性，以及下列動作︰

-   **便捷鍵**– 安全地呼叫 Power BI Api （如更新的一節所述） 時使用的按鍵。
-   **使用者**– 具有管理員權限管理 Power BI 工作區集合透過 Azure 入口網站或 ARM API 的 Azure Active Directory (AAD) 使用者。
-   **地區**– 一部分佈建**工作區集合**中，您可以選取要佈建中的區域。 如需詳細資訊，請參閱[Azure 區域](https://azure.microsoft.com/regions/)。

## <a name="workspace"></a>工作區

工作**區**是 Power BI 內容，包括資料集、 報表和儀表板的容器。 工作**區**是當您第一次建立空的。 在 [預覽]，您會作者使用 Power BI Desktop 的所有內容及您要上傳至其中一項工作區使用[Power BI REST Api](http://docs.powerbi.apiary.io/reference)。

## <a name="using-workspace-collections-and-workspaces"></a>使用工作區集合和工作區
**工作區集合**和**工作區**是內容的容器的使用與組織中不論最佳的方式符合您建立的應用程式的設計。 會有許多不同的方式，您可能排列其中的內容。 您可以選擇將工作區中的所有內容，然後稍後使用應用程式權杖，進一步細分的內容，在您的客戶。 您也可以選擇保留所有的客戶個別的工作區中，有些它們之間的分隔。 或者，您可以選擇將使用者的區域，而不是由客戶組織。 此彈性的設計，可讓您選擇的最佳方式，來組織內容。

## <a name="cached-datasets"></a>快取資料集

可在預覽中設定了快取資料集。  不過，您無法重新整理快取的資料後，已載入**Microsoft Power BI 內嵌**。

## <a name="authentication-and-authorization-with-app-tokens"></a>驗證與應用程式權杖與授權

**Microsoft Power BI 內嵌**延後應用程式中執行所有必要的使用者驗證與授權。 沒有明確的需求，您的使用者會客戶的 Azure Active Directory (Azure AD)。  您的應用程式來表示**Microsoft Power BI 內嵌**的授權，以呈現 Power BI 報表使用**應用程式驗證權杖 （應用程式權杖）**。  視您的應用程式想要呈現報表時，會建立這些**權杖應用程式**。  請參閱[應用程式權杖](power-bi-embedded-get-started-sample.md#key-flow)。

![](media\powerbi-embedded-whats-is\app-tokens.png)

**應用程式驗證權杖 （應用程式權杖）**用來驗證**Microsoft Power BI 內嵌**。  有三種類型的**應用程式權杖**︰

1.  佈建權杖-佈建**工作區集合**中的新的**工作區**時，使用
2.  開發權杖-時撥打電話至**Power BI REST Api**直接使用
3.  內嵌權杖-撥打電話來轉譯中內嵌的 iframe 的報表時，使用

這些權杖適用於各階段與**Microsoft Power BI 內嵌**互動。  權杖的設計，好讓您可以從您的應用程式委派權限，Power bi。 如需詳細資訊，請參閱[應用程式 Token 流程](power-bi-embedded-app-token-flow.md)。

## <a name="see-also"></a>另請參閱
- [Microsoft Power BI 內嵌的常見案例](power-bi-embedded-scenarios.md)
- [開始使用 Microsoft Power BI 內嵌](power-bi-embedded-get-started.md)
