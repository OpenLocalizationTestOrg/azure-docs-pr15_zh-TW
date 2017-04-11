<properties
   pageTitle="若要從 Azure AD 取得存取權杖使用用戶端判斷提示 |Microsoft Azure"
   description="如何從 Azure AD 取得存取權杖使用用戶端判斷提示。"
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

# <a name="using-client-assertion-to-get-access-tokens-from-azure-ad"></a>若要從 Azure AD 取得存取權杖使用用戶端判斷提示

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文是[一系列的一部分]。 此外還有隨附這一系列完成[範例應用程式]。

## <a name="background"></a>背景

當使用授權的程式碼流程或混合式流程中 OpenID 連線，用戶端交換存取權杖授權程式碼。 在此步驟中，用戶端已進行自我驗證至伺服器。

![用戶端密碼](media/guidance-multitenant-identity/client-secret.png)

驗證用戶端的其中一個方法是使用用戶端密碼。 年代如何[Tailspin 問卷][Surveys]應用程式的預設設定。

以下是從用戶端範例要求 IDP，要求存取權杖。 請注意`client_secret`參數。

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_secret=i3Bf12Dn...
  &grant_type=authorization_code
  &code=PG8wJG6Y...
```

私人是只是字串，您必須確定不遺漏的值。 最佳作法是保留從來源控制的用戶端密碼。 當您部署至 Azure 時，可將密碼儲存在[應用程式設定][configure-web-app]。

然而，有權存取 Azure 訂閱的任何人都可以檢視應用程式設定。 此外，一定會有必須檢查機密資料來源控制項 （例如，在部署指令碼），共用方式就是電子郵件，依此類推。

如需額外的安全性，您可以使用[用戶端判斷提示]，而不是用戶端密碼。 使用用戶端判斷提示，用戶端使用 X.509 憑證證明權杖要求來自於用戶端。 用戶端憑證已安裝於網頁伺服器。 通常來說，就會更容易超過，確定沒有人不小心即可在用戶端私人憑證，限制存取。 如需在 web 應用程式中設定憑證的詳細資訊，請參閱[Azure 網站應用程式中使用的憑證][using-certs-in-websites]

以下是使用用戶端判斷提示的權杖要求︰

```
POST https://login.microsoftonline.com/b9bd2162xxx/oauth2/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

resource=https://tailspin.onmicrosoft.com/surveys.webapi
  &client_id=87df91dc-63de-4765-8701-b59cc8bd9e11
  &client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer
  &client_assertion=eyJhbGci...
  &grant_type=authorization_code
  &code= PG8wJG6Y...
```

請注意，`client_secret`不再使用參數。 不過，`client_assertion`參數包含使用用戶端憑證簽署 JWT 權杖。 `client_assertion_type`參數會指定的判斷提示類型&mdash;在此例 JWT 權杖。 在伺服器驗證 JWT 權杖。 如果 JWT 權杖無效，則權杖要求會傳回錯誤。

> [AZURE.NOTE] X.509 憑證不是唯一的形式的用戶端判斷提示;我們著重於其以下因為 Azure AD 支援。

## <a name="using-client-assertion-in-the-surveys-application"></a>問卷應用程式中使用用戶端判斷提示

本節說明如何設定 Tailspin 問卷應用程式使用用戶端判斷提示。 按照這些步驟，就會產生適用於開發、 自我簽署的憑證，但不是生產。

1. 執行 PowerShell 指令碼[/Scripts/Setup-KeyVault.ps1] [ Setup-KeyVault] ，如下所示︰

    ```
    .\Setup-KeyVault.ps -Subject [subject]
    ```

    針對`Subject`參數，輸入任何名稱，例如 「 surveysapp 」。 指令碼會產生自我簽署的憑證，並將它儲存在 「 目前使用者/個人 」 憑證存放區。

2. 指令碼的輸出是 JSON 片段。 新增此應用程式顯示的 web 應用程式，如下所示︰

    1. 登入[Azure 管理入口網站][azure-management-portal]並瀏覽至您的 Azure AD 目錄。

    2. 按一下 [**應用程式**]。

    3. 選取 [問卷應用程式]。

    4.  按一下 [**管理資訊清單**，然後選取 [**下載資訊清單**。

    5.  開啟文字編輯器中的資訊清單 JSON 檔案。 貼到指令碼的輸出`keyCredentials`屬性。 應該看起來如下︰

        ```    
        "keyCredentials": [
            {
              "type": "AsymmetricX509Cert",
              "usage": "Verify",
              "keyId": "29d4f7db-0539-455e-b708-....",
              "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
              "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
            }
          ],
         ```

    6.  儲存變更至 JSON 檔案。

    7.  回到入口網站。 按一下 [**管理資訊清單** > **上傳資訊清單**及上傳 JSON 檔案。

3. 執行下列命令以取得指紋的憑證。

    ```
    certutil -store -user my [subject]
    ```

    位置`[subject]`是您指定的主旨中的 PowerShell 指令碼的值。 指紋會列在 「 憑證 Hash(sha1) 」。 移除的十六進位數字之間的空格。

4. 更新您的應用程式密碼。 在方案總管中，以滑鼠右鍵按一下 Tailspin.Surveys.Web 專案，然後選取 [**管理使用者的機密資訊**。 新增項目 」 Asymmetric 」 在 「 AzureAd 」，如下所示︰

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys application client ID]",
        // "ClientSecret": "[client secret]",  << Delete this entry
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Survey.WebAPI application]",
        // new:
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint]",  // Example: "105b2ff3bc842c53582661716db1b7cdc6b43ec9"
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "Redis": {
        "Configuration": "[Redis connection string]"
      }
    }
    ```

    您必須設定`ValidationRequired`為 false，因為憑證不是已簽署的根的 CA 授權。 實際使用的憑證簽署的 CA 授權及設定`ValidationRequired`為 true。

    刪除的項目`ClientSecret`，因為它，不需要用戶端判斷提示。

5. 在 Startup.cs，找出登錄的程式碼`ICredentialService`。 取消註解的使用線條`CertificateCredentialService`，和註解的使用線條`ClientCredentialService`:

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

在執行階段，web 應用程式會讀取憑證存放區的憑證。 在 web 應用程式以同一台機器上必須安裝憑證。

## <a name="next-steps"></a>後續步驟

- 閱讀本系列下一個文章︰[使用 Azure 金鑰保存庫保護應用程式的機密資訊][key vault]


<!-- Links -->
[configure-web-app]: ../app-service-web/web-sites-configure.md
[azure-management-portal]: https://manage.windowsazure.com
[用戶端判斷提示]: https://tools.ietf.org/html/rfc7521
[key vault]: guidance-multitenant-identity-keyvault.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[using-certs-in-websites]: https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/
[一系列的一部分]: guidance-multitenant-identity.md
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
