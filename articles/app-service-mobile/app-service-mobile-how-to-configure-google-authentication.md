<properties
    pageTitle="如何設定 Google 驗證您的應用程式服務應用程式"
    description="瞭解如何設定 Google 驗證您的應用程式服務應用程式。"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-google-login"></a>如何設定您的應用程式服務應用程式使用 Google 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定驗證提供者為使用 Google Azure 應用程式服務。

若要完成本主題中的程序，您必須通過驗證的電子郵件地址的 Google 帳戶。 若要建立新的 Google 帳戶，請移至[accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)。

## <a name="register"></a>登錄 Google 應用程式

1. 登入至[Azure 入口網站]，並瀏覽至您的應用程式。 複製您的**URL**，您稍後用來設定您的 Google 應用程式。

2. 瀏覽至[Google api](http://go.microsoft.com/fwlink/p/?LinkId=268303)網站、 您的 Google 帳戶認證登入、 按一下 [**建立專案**、 提供**專案名稱**，然後按一下 [**建立**]。

3. 在**社交 Api**底下，按一下**Google + API** ，然後**啟用**。

4. 在左側導覽畫面中，**認證** > **OAuth 同意畫面**，然後選取您的**電子郵件地址**、 輸入**產品名稱**，並按一下 [**儲存**]。

5. 在 [**認證**] 索引標籤中，按一下 [**建立認證** > **OAuth 用戶端識別碼**，然後選取 [ **Web 應用程式**。

6. 貼上您先前複製到**授權 JavaScript 來源**，應用程式服務**的 URL** ，然後將您重新導向 URI 貼到**授權重新導向 URI**]。 重新導向 URI 的路徑， _/.auth/login/google/callback_加上的應用程式的 URL。 例如， `https://contoso.azurewebsites.net/.auth/login/google/callback`。 請確認您使用的 HTTPS 配置。 然後按一下 [**建立**。

7. 在下一個畫面上，記下的 [用戶端識別碼和用戶端私人的值。


    > [AZURE.IMPORTANT]
    用戶端祕訣很重要的安全性認證。 不與任何共用這個密碼或散發它在用戶端應用程式。


## <a name="secrets"></a>新增 Google 應用程式的資訊

8. 回[Azure 入口網站]中，瀏覽至您的應用程式。 按一下 [**設定**]，然後**驗證 / 授權**。

9. 如果驗證 / 未啟用授權功能，請開啟切換到**上**。

10. 按一下 [ **Google**]。 貼上先前，取得應用程式識別碼] 和 [應用程式密碼值，您也可以啟用任何應用程式所需的範圍。 然後按一下**[確定]**。

    ![][1]

    根據預設，應用程式服務會提供驗證，但不會限制您的網站內容和 Api 未經授權的存取。 在您的應用程式碼中，您就必須授權的使用者。

17. （選用）若要限制存取至您的網站，以 Google 通過驗證的使用者，請**google**設定**要求未經驗證時採取的動作**。 這需要驗證所有要求，並在所有未經驗證的要求重新導向至 Google 驗證。

12. 按一下 [**儲存**]。

您已準備好使用 Google 應用程式中的驗證。

## <a name="related-content"></a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure 入口網站]: https://portal.azure.com/

