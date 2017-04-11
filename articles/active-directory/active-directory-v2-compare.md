<properties
    pageTitle="Azure AD v2.0 端點 |Microsoft Azure"
    description="比較原始 Azure AD 和 v2.0 端點。"
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
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="whats-different-about-the-v20-endpoint"></a>什麼是 v2.0 端點之間的差異？

如果您已經熟悉 Azure Active Directory 或具有過去的 Azure AD 與整合應用程式時，可能會有一些不同之處，在您不希望 v2.0 端點。  這份文件通話時，您瞭解差異。

> [AZURE.NOTE]
    並非所有的 Azure Active Directory 案例與功能支援 v2.0 端點。  若要判斷是否您應該使用 v2.0 端點，請閱讀有關[v2.0 限制](active-directory-v2-limitations.md)。


## <a name="microsoft-accounts-and-azure-ad-accounts"></a>Microsoft 帳戶和 Azure AD 帳戶
v2.0 端點允許開發人員撰寫接受登入 Microsoft 帳戶和 Azure AD 帳戶，使用單一驗證端點的應用程式。  這可讓您撰寫您的應用程式完全帳戶無關;可能不知道使用者登入的帳戶類型。  當然，您*可以*讓您的應用程式中的特定的工作階段，所使用的帳戶類型知道，但您不需要。

例如，如果您的應用程式呼叫[Microsoft Graph](https://graph.microsoft.io)，一些額外的功能和資料將可供企業使用者，例如他們的 SharePoint 網站或目錄的資料。  但的多個動作，例如[讀取使用者的郵件](https://graph.microsoft.io/docs/api-reference/v1.0/resources/message)、 撰寫程式碼可以完全相同的 Microsoft 帳戶和 Azure AD 帳戶。  

使用 Microsoft 帳戶和 Azure AD 帳戶整合應用程式現在一個簡單的程序。  您可以使用一組端點、 單一文件庫和單一應用程式註冊消費者和企業版的世界第一存取。  若要進一步瞭解 v2.0 端點，請參閱[的概觀](active-directory-appmodel-v2-overview.md)。


## <a name="new-app-registration-portal"></a>新的應用程式註冊入口網站
v2.0 端點只能登錄中的新位置︰ [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)。  這是您可以在哪裡取得應用程式識別碼，入口網站，自訂您的應用程式登入頁面及其他內容的外觀。  您只需要存取入口網站是一個可的 Microsoft 帳戶-個人或工作/學校帳戶。  

我們會繼續加入此應用程式註冊入口網站中的多的功能，一段時間。  用意是此入口網站，都可以在哪裡管理一切與您的 Microsoft 應用程式的新位置。


## <a name="one-app-id-for-all-platforms"></a>所有的平台的其中一個應用程式識別碼
在原始的 Azure Active Directory 服務中，您可能已經註冊單一專案的數種不同的應用程式。  您所強制使用另一個應用程式進行之註冊您的原生的用戶端和 web 應用程式︰

![舊的應用程式註冊 UI](../media/active-directory-v2-flows/old_app_registration.PNG)

例如，如果您建立的網站和 iOS 應用程式，您必須分別進行註冊使用兩個不同的應用程式識別碼。  如果您的網站和後端網頁 api，您可能已經註冊每個為不同的應用程式中 Azure AD。  如果您有 iOS 應用程式及 Android 應用程式，您也可能已登錄兩個不同的應用程式。  

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

現在，您只需要是單一應用程式註冊和單一應用程式識別碼的每個專案。  您可以將數個 「 平台 」 新增至每個專案，並針對每個您新增的平台提供適當的資料。  當然，您可以建立要根據您的需求，但是大多數情況下只有一個應用程式識別碼應該必要任意數目的應用程式。

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

我們的目的是這會導致更簡化的應用程式管理及開發經驗，並建立單一專案，您可能會處理其他合併的檢視。


## <a name="scopes-not-resources"></a>範圍，不資源
在原始 Azure AD 服務中，可以為**資源**或收件者的權杖的行為應用程式。  資源可定義多個**範圍**或**oAuth2Permissions**瞭解它，讓用戶端應用程式要求給該資源的一組特定範圍的權杖。  請考慮 Azure AD 圖形 API 作為資源的範例︰

- 資源識別碼] 或 [ `AppID URI`:`https://graph.windows.net/`
- 範圍，或`OAuth2Permissions`: `Directory.Read`，`Directory.Write`等。  

這適用於 v2.0 結束點。  仍會在應用程式，可以為資源定義範圍，並會以識別 URI。  用戶端應用程式仍可以要求存取權的範圍。  不過，已變更的用戶端要求這些權限的方式。  過去 OAuth 2.0 授權要求 Azure AD 可能會有看起來像︰

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

**資源**參數指示哪一個資源的用戶端應用程式要求的授權。  Azure AD 計算 Azure 入口網站中的靜態設定為基礎，因此發出的權杖的應用程式所需的權限。  現在，相同的 OAuth 2.0 授權要求看起來像︰

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

在 [**範圍**參數指出哪一個資源及位置的權限應用程式要求的授權。 所需的資源仍在邀請中非常展示-它只包含在每個範圍參數值。  使用範圍參數以這種方式可讓更多標準 OAuth 2.0 規格 v2.0 端點，並深入分析對齊常見產業作法。  也可讓應用程式，即可執行[遞增同意](#incremental-and-dynamic-consent)下, 一節所述。

## <a name="incremental-and-dynamic-consent"></a>遞增和動態同意
註冊推出 Azure AD 中的應用程式中 Azure 入口網站中，指定其所需的 OAuth 2.0 權限，在應用程式建立時所需的服務︰

![權限註冊 UI](../media/active-directory-v2-flows/app_reg_permissions.PNG)

**靜態地**設定已應用程式所需的權限。  雖然這允許存在於 Azure 入口網站中的應用程式的設定，並保留部份和簡易的程式碼，它會提供適用於開發人員幾個問題︰

- 應用程式必須知道所有的權限，就需要在應用程式建立時間。  新增一段時間的權限時發生困難。
- 應用程式必須知道的所有資源它也能存取提前。  很難建立無法存取任意數目的資源的應用程式。
- 應用程式必須要求其曾在使用者的第一個登入所需的權限。  在某些情況下這導致很長的權限，因此建議從核准初始登入應用程式的存取權的使用者清單。

您可以使用 v2.0 端點，指定您的應用程式**以動態方式**，在執行階段，必須在應用程式的一般使用的權限。  若要這麼做，您可以指定您的應用程式時所需要的任何指定的點時間在範圍`scope`的授權要求參數︰

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

若要閱讀 Azure AD 使用者的目錄資料，以及寫入其目錄中的資料的應用程式上述要求權限。  如果使用者有同意這些權限，此特定應用程式的過去，他們會直接輸入其認證，登入應用程式。  如果使用者有不同意這些權限的任何，v2.0 端點會要求使用者同意這些權限。  若要瞭解更多，您可以閱讀[權限、 同意及範圍](active-directory-v2-scopes.md)。

允許應用程式，以動態方式透過要求權限`scope`參數可讓您的使用者體驗的完全控制權。  如果您想要您可以選擇的 frontload 您同意體驗，並要求所有的權限，在單一初始的授權要求。  或者，如果您的應用程式需要大量的權限，您可以選擇這些權限向使用者收集從屬參照，嘗試使用一段時間的特定功能，您的應用程式。

## <a name="well-known-scopes"></a>已知的範圍

#### <a name="offline-access"></a>離線存取
v2.0 端點可能需要新的已知權限使用的應用程式-`offline_access`範圍。  如果需要存取資源的使用者代表一段時間，即使使用者可能不會積極使用應用程式的要求此權限必須所有應用程式。  `offline_access`範圍會顯示為同意對話方塊中，使用者 「 存取您的資料離線 」，這必須同意使用者。  要求`offline_access`權限可讓您從 v2.0 端點收到 OAuth 2.0 refresh_tokens 的 web 應用程式。  Refresh_tokens 久，而新 OAuth 2.0 access_tokens 可以交換長時間的存取權。  

如果您的應用程式不要求`offline_access`範圍，就不會收到 refresh_tokens。  這表示，當您兌換 authorization_code [OAuth 2.0 授權的程式碼流程](active-directory-v2-protocols.md#oauth2-authorization-code-flow)中，您只會收到回從 access_token`/token`結束點。  該 access_token 仍會維持有效的一段時間 （通常是一小時），但最後便會到期。  於的時間，您的應用程式中的點需要將使用者導向返回`/authorize`擷取新的 authorization_code 結束點。  在此重新導向]，使用者可能或可能不需要再次輸入其認證，或重新同意權限，而定的應用程式類型。

若要進一步瞭解 OAuth 2.0 與 refresh_tokens，access_tokens，請查看[v2.0 通訊協定參照](active-directory-v2-protocols.md)。

#### <a name="openid-profile--email"></a>OpenID、 設定檔及電子郵件

在原始的 Azure Active Directory 服務中的最基本 OpenID 連線登入流程會提供豐富的使用者在產生 id_token 中的相關資訊。  在 id_token 宣告可以包含使用者的名稱、 慣用的使用者名稱、 電子郵件地址、 物件識別碼等等。

現在，我們會限制資訊的`openid`範圍提供您的應用程式存取權。  'Openid' 範圍將只允許您的應用程式登入]，使用者並接收特定應用程式的使用者識別碼。  如果您想要取得相關應用程式中的使用者的個人識別資訊 (PII)，您的應用程式必須要求其他權限的使用者。  我們會介紹新的兩個領域 –`email`和`profile`範圍-可讓您執行此作業。

`email`範圍是非常簡單，其可讓您的應用程式存取權的使用者主要的電子郵件地址，透過`email`中 id_token 宣告。  `profile`範圍提供所有其他基本的使用者資訊 – 其名稱、 慣用的使用者名稱，您應用程式存取物件識別碼，依此類推。

這個選項可讓您以最小揭露方式應用程式的程式碼，因此您僅可以要求使用者的應用程式，來完成工作所需的資訊集。  如需有關這些範圍的詳細資訊，請參閱[v2.0 範圍參照](active-directory-v2-scopes.md)。 

## <a name="token-claims"></a>權杖宣告

在發出 v2.0 端點的權杖宣告不會發出推出的權杖相同 Azure AD 結束點-移轉至新的服務應用程式不應該假設特定的宣告會存在於 id_tokens 或 access_tokens。   權杖發出 v2.0 端點的 OAuth 2.0 及 OpenID 連線的規格，符合，但可能會依照不同的語意與推出 Azure AD 服務。

若要瞭解特定 v2.0 權杖中所發出的宣告，請參閱[v2.0 權杖參照](active-directory-v2-tokens.md)。

## <a name="limitations"></a>限制
有一些限制時要注意的使用 v2.0 點。  請參閱若要查看是否任何這些限制會套用到特定的案例[v2.0 限制的文件](active-directory-v2-limitations.md)。
