<properties
    pageTitle="如何設定 Facebook 驗證您的應用程式服務應用程式"
    description="瞭解如何設定 Facebook 驗證您的應用程式服務應用程式。"
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

# <a name="how-to-configure-your-app-service-application-to-use-facebook-login"></a>如何設定您的應用程式服務應用程式使用 Facebook 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定驗證提供者為使用 Facebook Azure 應用程式服務。

若要完成本主題中的程序，您必須具備驗證電子郵件地址及的行動電話號碼的 Facebook 帳戶。 若要建立新的 Facebook 帳戶，請移至[facebook.com]。

## <a name="register"></a>與 Facebook 註冊您的應用程式

1. 登入至[Azure 入口網站]，並瀏覽至您的應用程式。 複製您的**URL**。 您將使用此設定您的 Facebook 應用程式。

2. 在另一個瀏覽器視窗中，瀏覽至[Facebook 開發人員]網站，然後使用您的 Facebook 帳戶認證登入。

3. （選用）如果您有不已註冊，請按一下 [**應用程式** > **註冊為開發人員**，然後接受原則，並依照註冊步驟。

4. 按一下 [**我的應用程式** > **新增新的應用程式** > **網站** > **略過，並建立應用程式識別碼**。 

5. 在 [**顯示名稱**] 輸入您的應用程式的唯一名稱、 輸入您的**連絡人電子郵件**、 選擇一個**類別**，應用程式，按一下 [**建立應用程式識別碼**，完成安全性核取。 這會帶您到新的 Facebook 應用程式開發人員儀表板。

6. 在 「 Facebook 登入] 底下，按一下 [**開始**]。 新增您的應用程式**重新導向 URI** **有效 OAuth 重新導向 Uri**，然後按一下 [**儲存變更**。 

    > [AZURE.NOTE] 您重新導向 URI 的路徑， _/.auth/login/facebook/callback_加上的應用程式的 URL。 例如， `https://contoso.azurewebsites.net/.auth/login/facebook/callback`。 請確認您使用的 HTTPS 配置。

6. 在左側導覽畫面中，按一下 [**設定**]。 在 [**應用程式密碼**] 欄位中，按一下 [**顯示**，提供您的密碼，如果要求，然後記下的 [**應用程式識別碼**] 和 [**應用程式密碼**的值。 您使用這些稍後 Azure 中設定您的應用程式。

    > [AZURE.IMPORTANT] 應用程式密碼是重要的安全性認證。 不與任何共用這個密碼或散發它在用戶端應用程式。

7. 用來註冊的應用程式的 Facebook 帳戶是應用程式的系統管理員。 此時，只有系統管理員可以登入這個應用程式。 若要驗證的其他 Facebook 帳戶，請按一下 [**應用程式檢視**並啟用**設為 < 您應用程式的名稱 > 公開**啟用一般使用 Facebook 驗證的公開存取。

## <a name="secrets"></a>新增 Facebook 應用程式的資訊

1. 回[Azure 入口網站]中，瀏覽至您的應用程式。 按一下 [**設定** > **驗證 / 授權**，並確認已經**開啟**的**應用程式服務驗證**。

2. 按一下**Facebook**、 貼上先前取得應用程式識別碼] 和 [應用程式密碼值、 您也可以啟用任何應用程式所需的範圍，然後按一下**[確定]**。

    ![][0]

    根據預設，應用程式服務會提供驗證，但不會限制您的網站內容和 Api 未經授權的存取。 在您的應用程式碼中，您就必須授權的使用者。

3. （選用）若要限制存取至您的網站 Facebook 通過驗證的使用者，請至**Facebook**設定**要求未經驗證時採取的動作**。 這需要驗證所有要求，並所有未經驗證的要求重新導向至 Facebook 進行驗證。

4. 完成設定驗證，請按一下 [**儲存**]。

您已準備好要用於驗證您的應用程式中的 Facebook。

## <a name="related-content"></a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook 開發人員]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure 入口網站]: https://portal.azure.com/
