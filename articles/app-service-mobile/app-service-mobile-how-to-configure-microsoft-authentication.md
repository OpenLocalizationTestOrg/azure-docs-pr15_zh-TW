<properties
    pageTitle="如何設定應用程式服務應用程式的 Microsoft 帳戶驗證"
    description="瞭解如何設定您的應用程式服務應用程式的 Microsoft 帳戶驗證。"
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-microsoft-account-login"></a>如何設定您的應用程式服務應用程式，來使用 Microsoft 帳戶登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定驗證提供者為使用 Microsoft 帳戶 Azure 應用程式服務。 

## <a name="register-microsoft-account"></a>註冊您的應用程式使用的 Microsoft 帳戶

1. 登入至[Azure 入口網站]，並瀏覽至您的應用程式。 複製您的**URL**，您用來設定您的應用程式使用的 Microsoft 帳戶的更新版本。

2. 瀏覽至 [[我的應用程式]頁面的 Microsoft 帳戶開發人員中心] 中，必要時使用您的 Microsoft 帳戶登入。

3. 按一下 [**新增應用程式**，然後輸入應用程式名稱，然後按一下 [**建立應用程式**。

4. 記下該**應用程式識別碼**，您會需要更新版本。 

5. 在 「 平台 」，按一下 [**新增平台**，選取 [網站]。

6. 在 「 重新導向 Uri] 底下，提供您的應用程式的結束點，然後按一下 [**儲存**]。 
 
    >[AZURE.NOTE]您重新導向 URI 的路徑， _/.auth/login/microsoftaccount/callback_加上的應用程式的 URL。 例如， `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`。   
    >請確認您使用的 HTTPS 配置。

7. 下 」 應用程式密碼]，按一下 [**產生新的密碼**]。 請記下顯示的值。 當您離開的頁面時，它會不會再顯示。


    > [AZURE.IMPORTANT] 密碼不重要的安全性認證。 不使用的任何人共用的密碼或散發它在用戶端應用程式。

## <a name="secrets"></a>應用程式服務應用程式新增 Microsoft 帳戶資訊

1. 傳回在[Azure 入口網站]，瀏覽至您的應用程式，按一下 [**設定** > **驗證 / 授權**。

2. 如果驗證 / 未啟用授權功能，**將其開啟**。

3. 按一下 [ **Microsoft 帳戶**]。 貼上先前，取得應用程式識別碼和密碼值，您也可以啟用任何應用程式所需的範圍。 然後按一下**[確定]**。

    ![][1]

    根據預設，應用程式服務會提供驗證，但不會限制您的網站內容和 Api 未經授權的存取。 在您的應用程式碼中，您就必須授權的使用者。

4. （選用）若要限制存取至您的網站，由 Microsoft 帳戶驗證的使用者，請將**要求未經驗證時採取動作**的**Microsoft**帳戶。 這需要驗證所有要求，並在所有未經驗證的要求重新導向至 Microsoft 帳戶驗證。

5. 按一下 [**儲存**]。

您已準備好要用於驗證您的應用程式中的 Microsoft 帳戶。

## <a name="related-content"></a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[我的應用程式]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure 入口網站]: https://portal.azure.com/
