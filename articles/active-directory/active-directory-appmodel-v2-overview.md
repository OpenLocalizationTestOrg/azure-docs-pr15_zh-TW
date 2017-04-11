<properties
    pageTitle="v2.0 端點概觀 |Microsoft Azure"
    description="簡介如何建置應用程式搭配使用的 Microsoft 帳戶和 Azure Active Directory 登入。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>登入 Microsoft 帳戶和 Azure AD 使用者在單一應用程式

過去，想要支援 Microsoft 帳戶和 Azure Active Directory 應用程式開發人員需要與兩個不同的系統整合。  現在，我們已推出新的驗證 API 版本可讓您登入使用兩種類型的帳戶使用 Azure AD 系統的使用者。  這個交集的驗證系統稱為**v2.0 結束點**。  與 v2.0 端點，一個簡單的整合功能可讓您達到橫跨數百萬種個人及工作/學校帳戶的使用者對象。

使用 v2.0 端點的應用程式，也可以使用 REST Api 從[Microsoft Graph](https://graph.microsoft.io)和[Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)使用任一類型的帳戶。

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>快速入門
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

選擇您最愛的平台從下列清單來建立應用程式使用我們開啟來源文件庫與架構。  或者，您可以使用我們 OAuth 2.0 與 OpenID 連線的通訊協定說明文件以傳送及接收通訊協定訊息直接不使用驗證文件庫。

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>新增功能
概念性資訊會有幫助瞭解什麼是與什麼無法與 v2.0 端點。

- 深入瞭解[應用程式，您可以建立含 v2.0 結束點類型](active-directory-v2-flows.md)。
- 瞭解[限制與限制，限制](active-directory-v2-limitations.md)為 v2.0 結束點。
- 我們已最近新增[管理員限制範圍](active-directory-v2-scopes.md)和[認證授與 OAuth2 用戶端](active-directory-v2-protocols-oauth-client-creds.md)支援。  試試看 ！

## <a name="reference"></a>參照
以下連結將會適合用來探索中深度的平台︰

- 建立 2016: [Microsoft 身分識別快速入門︰ 企業成績登入您的應用程式](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- 取得說明堆疊溢位使用[azure active 目錄](http://stackoverflow.com/questions/tagged/azure-active-directory)或[adal](http://stackoverflow.com/questions/tagged/adal)標籤。
- [v2.0 通訊協定參照](active-directory-v2-protocols.md)
- [v2.0 權杖參照](active-directory-v2-tokens.md)
- [v2.0 文件庫參考](active-directory-v2-libraries.md)
- [範圍及同意 v2.0 端點](active-directory-v2-scopes.md)
- [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Office 365 REST API 參考](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)