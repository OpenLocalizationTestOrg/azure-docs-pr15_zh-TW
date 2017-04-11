<properties
   pageTitle="使用 multitenant 應用程式中的 [宣告型身分識別 |Microsoft Azure"
   description="使用宣告發行者驗證與授權的方式"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/23/2016"
   ms.author="mwasson"/>

# <a name="working-with-claims-based-identities-in-multitenant-applications"></a>使用宣告式 multitenant 應用程式中的身分識別

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文是[一系列的一部分]。 此外還有隨附這一系列完成[範例應用程式]。

## <a name="claims-in-azure-ad"></a>Azure AD 中的宣告

當使用者登入時，Azure AD 傳送包含一組使用者的相關的宣告的識別碼權杖。 宣告是只要一段資訊，以金鑰/值組。 例如， `email` = `bob@contoso.com`。  宣告有發行者&mdash;在此例中 Azure AD&mdash;已授權的使用者，並建立宣告的實體。 因為您信任的發行者，您可以信任宣告。 （相反地，如果您不信任的發行者，否則不信任宣告 ！）

在高的層級︰

1.  使用者進行驗證。
2.  IDP 傳送一組宣告。
3.  應用程式會標準化，或將宣告 （可省略）。
4.  應用程式使用宣告授權決策。

在 OpenID 連線，您收到的宣告一組控制由驗證要求的[範圍參數]。 不過，Azure AD 問題有限的宣告透過 OpenID 連線。請參閱[支援的權杖和宣告類型]。 如果您想要的使用者的詳細資訊，您會需要使用 Azure AD 圖形 API。

以下是從應用程式可能會通常關注的資訊的 AAD 宣告部分︰

宣告識別碼權杖類型 |    描述
-----------------------|--------------
aud | 誰發出權杖。 這是應用程式的用戶端識別碼 一般而言，您應該不需要擔心此宣告，因為介軟體會自動驗證。 範例︰`"91464657-d17a-4327-91f3-2ed99386406f"`
群組   | AAD 的使用者群組成員的清單。 範例︰`["93e8f556-8661-4955-87b6-890bc043c30f", "fc781505-18ef-4a31-a7d5-7d931d7b857e"]`
iss  | OIDC 權杖[發行者]。 範例︰`https://sts.windows.net/b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4/`
名稱    | 使用者的顯示名稱。 範例︰`"Alice A."`
oid | 使用者在 AAD 中物件識別碼。 此值是使用者不變，非可重複使用識別碼。 使用此值，而非電子郵件，為使用者; 的唯一識別碼可以變更電子郵件地址。 如果您在您的應用程式中使用 Azure AD 圖形 API，物件識別碼是用來查詢的設定檔資訊的值。 範例︰`"59f9d2dc-995a-4ddf-915e-b3bb314a7fa4"`
角色   | 應用程式角色的使用者清單。 範例︰`["SurveyCreator"]`
tid | 租用戶識別碼。 此值會位於 Azure AD 租用戶的唯一識別碼。 範例︰`"b9bd2162-77ac-4fb2-8254-5c36e9c0a9c4"`
unique_name | 使用者的人力可讀的顯示名稱。 範例︰`"alice@contoso.com"`
upn | 使用者主體名稱。 範例︰`"alice@contoso.com"`

下表列出宣告類型，顯示在識別碼權杖中。 在 ASP.NET 核心 1.0，OpenID 連線介軟體時，將轉換宣告類型的部分，填入使用者主體宣告集合︰

-   oid >`http://schemas.microsoft.com/identity/claims/objectidentifier`
-   tid >`http://schemas.microsoft.com/identity/claims/tenantid`
-   unique_name >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
-   upn >`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`

## <a name="claims-transformations"></a>宣告轉換

期間驗證流程，您可能要修改您從 IDP 取得宣告。 ASP.NET 核心 1.0，您可以從 OpenID 連線介軟體執行宣告轉換 eml **AuthenticationValidated**事件。 （請參閱[驗證事件]）。

您新增期間**AuthenticationValidated**任何宣告會儲存在工作階段驗證 cookie。 沒有取得放入 Azure AD 的上一步。

以下是一些範例宣告轉換︰

-   **宣告正規化**或讓宣告一致跨使用者。 這是如果您已準備宣告從多個 IDPs，可能會使用不同的宣告類型的類似的資訊。
例如，Azure AD 傳送 「 upn 」 宣告包含使用者的電子郵件。 其他 IDPs 可能會傳送 [電子郵件] 宣告。 下列程式碼會轉換為 「 以電子郵件傳送 」 宣告 「 upn 」 宣告︰

    ```csharp
    var email = principal.FindFirst(ClaimTypes.Upn)?.Value;
    if (!string.IsNullOrWhiteSpace(email))
    {
      identity.AddClaim(new Claim(ClaimTypes.Email, email));
    }
    ```

- 新增的宣告沒有顯示 [**預設宣告值** &mdash; ，例如，將使用者指派至預設角色。 在某些情況下，這可簡化授權邏輯。
- 新增**自訂宣告類型**與特定應用程式的使用者資訊。 例如，您可能會儲存部分使用者的相關資訊資料庫中。 您可以新增此資訊的自訂宣告驗證票證。 宣告會儲存於 cookie 中,，讓您只需要將它從登入工作階段每一次資料庫。 另一方面，您也想避免建立極大 cookie，讓您需要考慮取捨 cookie 大小與資料庫的查詢。   

宣告驗證流程完成後，會提供`HttpContext.User`。 此時，您應該將它們視為唯讀集合&mdash;例如，使用這些授權決策。

## <a name="issuer-validation"></a>發行者驗證
在 OpenID 連線，發行者宣告 (「 iss 」) 會識別發出識別碼權杖 IDP。 驗證發行者宣告符合實際發行者可 OIDC 驗證流程的一部分。 OIDC 介軟體會為您處理這個。

Azure AD，發行者] 值是唯一的每個 AD 租用戶 (`https://sts.windows.net/<tenantID>`)。 因此，應用程式應該進行額外的核取，以確保簽發者代表租用戶允許登入應用程式。

單一租用戶應用程式，您就可以檢查簽發者是您自己的租用戶。 事實上，OIDC 介軟體會自動預設值。 在多租用戶應用程式中，您必須允許多個發行者，對應到不同的租用戶。 以下是使用一般的方法︰

-   在 [OIDC 介軟體] 選項中，設定**ValidateIssuer**為 false。 這會關閉自動檢查。
-   當租用戶註冊時，儲存租用戶的學生和發行者 DB 您的使用者。
-   當使用者登入時，查詢在資料庫中的發行者。 如果找不到發行者，這表示該租用戶未註冊。 您可以導向到註冊] 頁面。
-  您也可以 blacklist 特定租用戶。例如的未付款訂閱的客戶。

如需詳細說明，請參閱[註冊和租用戶中 multitenant 應用程式的登入][signup]。

## <a name="using-claims-for-authorization"></a>使用宣告的授權

使用宣告，使用者的身分識別不再龐大的實體。 例如，使用者可能會有電子郵件地址、 電話號碼、 生日、 性別等。也許使用者的 IDP 儲存所有的這項資訊。 但時的使用者進行驗證，您通常會取得這些宣告為子集。 在此模式中，使用者的身分識別是只要的宣告集結。 當您決定使用者的授權，您會看到特定組的宣告。 換句話說，問題 」 可以使用者 X 執行動作 Y 」 最後會變成 」 會的使用者 X 有宣告 Z 」。

以下是一些基本模式檢查宣告。

-  若要查看的使用者都有特定的宣告，與特定的值︰

    ```csharp
    if (User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
    ```
    將此程式碼會檢查使用者是否具有角色宣告以 「 系統 」 的值。 正確處理當使用者有沒有角色宣告或多個角色宣告大小寫。

    **ClaimTypes**類別定義常用的宣告類型的常數。 不過，您可以使用任何字串值宣告類型。

-   當您那里最一個值，則您可以取得宣告型別單一值︰
    ```csharp
     string email = User.FindFirst(ClaimTypes.Email)?.Value;
    ```
-   若要取得宣告類型中所有的值︰

    ```csharp
     IEnumerable<Claim> groups = User.FindAll("groups");
    ```

如需詳細資訊，請參閱[multitenant 應用程式中的角色和資源授權][authorization]。

## <a name="next-steps"></a>後續步驟

- 閱讀本系列下一個文章︰[註冊和租用戶中 multitenant 應用程式的登入][signup]
- 深入瞭解[宣告式授權]的 ASP.NET 核心 1.0 文件

<!-- Links -->
[一系列的一部分]: guidance-multitenant-identity.md
[範圍參數]: http://nat.sakimura.org/2012/01/26/scopes-and-claims-in-openid-connect/
[支援的權杖和宣告類型]: ../active-directory/active-directory-token-and-claims.md
[發行者]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
[驗證事件]: guidance-multitenant-identity-authenticate.md#authentication-events
[signup]: guidance-multitenant-identity-signup.md
[宣告為基礎的授權]: https://docs.asp.net/en/latest/security/authorization/claims.html
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
[authorization]: guidance-multitenant-identity-authorize.md
