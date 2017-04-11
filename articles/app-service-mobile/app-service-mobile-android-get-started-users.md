<properties
    pageTitle="使用行動應用程式在 Android 上新增驗證 |Azure 應用程式服務"
    description="瞭解如何使用行動應用程式中 Azure 應用程式服務驗證使用者透過各種不同的身分識別提供者，包括 Google、 Facebook、 Twitter 和 Microsoft Android 應用程式。"
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>將驗證新增至您的 Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>摘要

在本教學課程中，您專案中新增驗證 todolist 快速入門 Android 使用支援的身分識別提供者。 本教學課程為基礎[開始 Mobile 應用程式使用]教學課程中，您必須先完成的。

##<a name="register"></a>註冊您的應用程式的驗證及設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制權限已驗證的使用者

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ 在 Android Studio 中，開啟的專案計劃您完成與[Mobile 應用程式快速入門]教學課程。 從 [**執行**] 功能表中按一下**執行應用程式**並確認應用程式啟動後，會引發處理的例外狀況的狀態碼 401 （未經授權）。

     因為應用程式嘗試存取後端非驗證的使用者，但_TodoItem_表格現在需要驗證，就會發生此例外狀況。

接下來，您可以更新驗證的使用者行動應用程式後端要求資源之前先將應用程式。

## <a name="add-authentication-to-the-app"></a>新增驗證應用程式

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>在用戶端的快取驗證權杖

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>後續步驟

現在您完成此基本驗證教學課程，請考慮繼續執行下列教學課程其中一項︰

+ [新增至您的 Android 應用程式的推播通知](app-service-mobile-android-get-started-push.md)瞭解如何設定您的行動應用程式後端使用 Azure 通知集線器傳送推入通知。

+ [啟用離線同步處理您的 Android 應用程式](app-service-mobile-android-get-started-offline-data.md)瞭解如何新增離線支援您使用行動應用程式後端的應用程式。 離線同步處理可讓使用者在行動應用程式與之互動&mdash;檢視、 新增或修改資料&mdash;即使沒有網路連線。



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[開始使用行動應用程式]: app-service-mobile-android-get-started.md
