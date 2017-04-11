<properties
    pageTitle="Azure Active Directory B2C︰ 概觀 |Microsoft Azure"
    description="開發與 Azure Active Directory B2C 消費者具應用程式"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-sign-up-and-sign-in-consumers-in-your-applications"></a>Azure Active Directory B2C︰ 登入並登入您的應用程式中的使用者皆

Azure Active Directory B2C 是消費者對外公開網頁和行動應用程式的完整雲端身分識別管理解決方案。 就是高度可用的全域服務縮放到數百個數百萬消費者身分識別。 Azure Active Directory B2C 內建企業級安全平台上，會保留您的應用程式與企業保護您的使用者能。

過去，應用程式開發人員想要登入並登入到應用程式的使用者皆會編寫自己的程式碼。 他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。 Azure Active Directory B2C 提供開發人員消費者身分識別管理整合協助應用程式的安全性、 標準的平台和豐富的可延伸原則更好的方法。 當您使用 Azure Active Directory B2C 時，使用者可以登入您的應用程式藉由使用其現有社交帳戶 （Facebook、 Google、 Amazon、 LinkedIn） 或建立新的認證 （電子郵件地址和密碼，或使用者名稱和密碼）;我們稱為 「 第二個 [本機帳戶。 」

## <a name="get-started"></a>快速入門

若要建立的應用程式，可接受消費者註冊和登入，您會需要先登錄應用程式的 Azure Active Directory B2C 租用戶。 使用在[建立 Azure AD B2C 租用戶](active-directory-b2c-get-started.md)中所述的步驟，以取得您自己的租用戶。

選擇傳送通訊協定訊息直接使用[OAuth 2.0](active-directory-b2c-reference-protocols.md#oauth2-authorization-code-flow)或[開啟識別碼連線](active-directory-b2c-reference-protocols.md#openid-connect-sign-in-flow)，或是您執行的工時使用我們的文件庫，您可以撰寫 Azure Active Directory B2C 服務應用程式。 下表中，選擇您最愛的平台，並開始使用。

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## <a name="whats-new"></a>新增功能

核取回來通常若要瞭解如何變更未來 Azure Active Directory B2C。 我們會也 tweet 相關的任何更新，使用@AzureAD。

- 瞭解關於我們[可延伸原則架構](active-directory-b2c-reference-policies.md)以及您可以建立及使用中應用程式原則的類型。
- 書籤次要的服務問題、 更新、 狀態及減輕通知我們[服務部落格](https://blogs.msdn.microsoft.com/azureadb2c/)。 繼續監視[Azure 狀態儀表板](https://azure.microsoft.com/status/)，也。
- 目前[服務限制的限制，與限制](active-directory-b2c-limitations.md)。
- 最後，[程式碼範例](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-b2c)使用 Azure AD B2C ASP.NET 核心。

## <a name="how-to-articles"></a>使用方法文章

瞭解如何使用特定的 Azure Active Directory B2C 功能︰

- 消費者具應用程式中設定、 [Google +](active-directory-b2c-setup-goog-app.md)、 [Microsoft 帳戶](active-directory-b2c-setup-msa-app.md)、 [Amazon](active-directory-b2c-setup-amzn-app.md)的[Facebook](active-directory-b2c-setup-fb-app.md)和[LinkedIn](active-directory-b2c-setup-li-app.md)帳戶使用。
- [使用收集您客戶的相關資訊的自訂屬性](active-directory-b2c-reference-custom-attr.md)。
- [消費者具應用程式中啟用 Azure 多重因素驗證](active-directory-b2c-reference-mfa.md)。
- [設定自助密碼重設您的消費者用](active-directory-b2c-reference-sspr.md)。
- [自訂外觀與風格的註冊，登入，與其他消費者具頁面](active-directory-b2c-reference-ui-customization.md)會由 Azure Active Directory B2C。
- 在您的 Azure Active Directory B2C 租用戶中[使用 Azure Active Directory 圖形 API 以程式設計方式建立、 讀取、 更新和刪除使用者皆](active-directory-b2c-devquickstarts-graph-dotnet.md)。

## <a name="next-steps"></a>後續步驟

以下連結將會適合用來探索深度服務︰

- 請參閱[Azure Active Directory B2C 價格資訊](https://azure.microsoft.com/pricing/details/active-directory-b2c/)。
- 取得說明使用[azure active 目錄](http://stackoverflow.com/questions/tagged/azure-active-directory)堆疊溢位或[adal](http://stackoverflow.com/questions/tagged/adal)標籤。
- 使用[使用者語音](https://feedback.azure.com/forums/169401-azure-active-directory/)提供您的想法，我們希望瞭解它們的想法 ！ 使用 「 「 AzureADB2C: 「 您的文章，以方便我們可以找到標題中。
- 檢閱[Azure AD B2C 通訊協定參照](active-directory-b2c-reference-protocols.md)。
- 檢閱[Azure AD B2C 權杖參照](active-directory-b2c-reference-tokens.md)。
- 閱讀[Azure Active Directory B2C 常見問題集](active-directory-b2c-faqs.md)。
- [支援的 Azure Active Directory B2C 要求的檔案](active-directory-b2c-support.md)。

## <a name="get-security-updates-for-our-products"></a>取得產品的安全性更新

我們建議您收到通知，造訪[本頁面](https://technet.microsoft.com/security/dd252948)訂閱安全性提示警示的安全性事件發生時。
