<properties
    pageTitle="如何設定 Twitter 驗證您的應用程式服務應用程式"
    description="瞭解如何設定 Twitter 驗證您的應用程式服務應用程式。"
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

# <a name="how-to-configure-your-app-service-application-to-use-twitter-login"></a>如何設定您的應用程式服務應用程式使用 Twitter 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定驗證提供者為使用 Twitter Azure 應用程式服務。

若要完成本主題中的程序，您必須具有驗證電子郵件地址和電話號碼的 Twitter 帳戶。 若要建立新的 Twitter 帳戶，請移至<a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>。

## <a name="register"> </a>Twitter 以註冊您的應用程式


1. 登入至[Azure 入口網站]，並瀏覽至您的應用程式。 複製您的**URL**。 您將使用此設定 Twitter 應用程式。

2. 瀏覽至[Twitter 開發人員]網站使用 Twitter 帳戶認證，登入，按一下 [**建立新的應用程式**。

3. 輸入**名稱**，以及新的應用程式的**描述**。 在 [**網站**] 的值的應用程式的**URL**貼上。 然後的**回撥 URL**中，貼上您先前複製的**回撥 URL** 。 這是您的行動應用程式閘道加上的路徑， _/.auth/login/twitter/callback_。 例如， `https://contoso.azurewebsites.net/.auth/login/twitter/callback`。 請確認您使用的 HTTPS 配置。

3.  在頁面底端，請閱讀並接受條款。 然後按一下 [**建立 Twitter 應用程式**。 此登錄應用程式顯示的應用程式詳細資料。

4. 按一下 [**設定**] 索引標籤，核取 [**允許要用來使用 Twitter 登入這個應用程式**，然後按一下 [**更新設定**。

5. 選取 [**索引鍵和存取權杖**] 索引標籤。 記下**消費者鍵 （API 按鍵）**和**消費者私人 （API 私人）**的值。

    > [AZURE.NOTE] 消費者祕訣很重要的安全性認證。 不與任何共用這個密碼或散發它使用您的應用程式。


## <a name="secrets"></a>新增 Twitter 應用程式的資訊

13. 回[Azure 入口網站]中，瀏覽至您的應用程式。 按一下 [**設定**]，然後**驗證 / 授權**。

14. 如果驗證 / 未啟用授權功能，請開啟切換到**上**。

15. 按一下 [ **Twitter**]。 貼上在應用程式識別碼與應用程式密碼先前取得的值。 然後按一下**[確定]**。

    ![][1]

    根據預設，應用程式服務會提供驗證，但不會限制您的網站內容和 Api 未經授權的存取。 在您的應用程式碼中，您就必須授權的使用者。

17. （選用）若要限制存取至您的網站，以 Twitter 通過驗證的使用者，請將**要求未經驗證時採取的動作**為**Twitter**。 這需要驗證所有要求，並在所有未經驗證的要求重新導向至 Twitter 驗證。

17. 按一下 [**儲存**]。

您已準備好進行驗證，在您的應用程式中使用 Twitter。

## <a name="related-content"></a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-twitter-authentication/app-service-twitter-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-twitter-authentication/mobile-app-twitter-settings.png

<!-- URLs. -->

[Twitter 開發人員]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Azure 入口網站]: https://portal.azure.com/
[xamarin]: ../app-services-mobile-app-xamarin-ios-get-started-users.md
