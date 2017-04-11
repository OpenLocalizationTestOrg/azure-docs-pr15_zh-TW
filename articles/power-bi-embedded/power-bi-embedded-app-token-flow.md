<properties
   pageTitle="驗證並使用 Power BI 內嵌授權"
   description="驗證並使用 Power BI 內嵌授權"
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

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>驗證並使用 Power BI 內嵌授權

內嵌 Power BI 服務使用**索引鍵**和**應用程式的權杖**進行驗證及授權，而不是明確的使用者驗證。 在此模式中，您的應用程式管理驗證與授權的使用者。 必要時，您的應用程式建立並傳送應用程式權杖，告訴我們的服務，來轉譯要求的報表。 此設計不需要用於 Azure Active Directory 驗證使用者與授權，您的應用程式，但您仍可以。

## <a name="two-ways-to-authenticate"></a>驗證的兩種方法

**索引鍵**-您可以使用按鍵 Power BI 內嵌 REST API 電話。 按鍵中找**Azure 入口網站**上**所有設定**]，然後按一下 [**便捷鍵**即可。 始終視您的金鑰，就好像密碼。 這些按鍵有請打電話給特定的工作區集合上的任何 REST API 的權限。

若要使用金鑰其他電話，新增下列授權標頭︰            

    Authorization: AppKey {your key}

**應用程式權杖**權杖適用於所有應用程式內嵌要求。 這些被設計用戶端，讓它們限制為單一報表，並設定到期時間的最佳作法是執行。

應用程式權杖是 JWT （JSON Web 權杖） 所簽署的其中一個索引鍵。

您的應用程式權杖可以包含下列宣告︰

| 宣告      | 描述        |
|--------------|------------|
| **ver**      | 應用程式權杖版本。 0.2.0 是最新版本。       |
| **aud**      | 權杖的收件的者。 Power BI 內嵌用於: 「 https://analysis.windows.net/powerbi/api 」。  |
| **iss**      |  字串，表示發出權杖的應用程式。    |
| **類型**     | 應用程式的權杖所建立的類型。 目前只支援的類型是**內嵌**。   |
| **wcn**      | 工作區集合名稱權杖會發出的。  |
| **wid**      | 工作區識別碼權杖會發出的。  |
| **移除**      | 報表識別碼權杖會發出的。     |
| **使用者名稱**（選用） |  這是字串，可協助您識別使用者套用 RLS 規則時使用 RLS。 |
| **角色**（選用）   |   字串，包含套用列層級安全性規則時，選取的角色。 如果傳遞一個以上的角色，他們應該傳遞螫針陣列。    |
| **exp**（選用）    |   指示的權杖便會到期的時間。 這些應該傳遞中為 Unix 加上時間戳記。   |
| **nbf**（選用）    |   指示權杖開始的正有效的時間。 這些應該傳遞中為 Unix 加上時間戳記。   |

範例應用程式權杖看起來像這樣︰

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


當解碼，它看起來像這樣︰

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>以下是流程的運作方式

1. 您的應用程式中複製的 API 金鑰。 您可以在**入口網站中 Azure**取得按鍵。

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. 權杖判斷宣告，而且有的到期時間。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. 權杖簽署與 API 便捷鍵。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. 若要檢視的報告使用者要求。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  權杖會驗證與 API 便捷鍵。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI 內嵌將報表傳送給使用者。

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

**Power BI 內嵌**將報表傳送給使用者後，使用者就可以自訂應用程式中檢視報表。 例如，如果您匯入[分析銷售資料 PBIX 範例](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)，範例 web 應用程式如下所示︰

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>另請參閱
- [開始使用 Microsoft Power BI 內嵌的範例](power-bi-embedded-get-started-sample.md)
- [Microsoft Power BI 內嵌的常見案例](power-bi-embedded-scenarios.md)
- [開始使用 Microsoft Power BI 內嵌](power-bi-embedded-get-started.md)
