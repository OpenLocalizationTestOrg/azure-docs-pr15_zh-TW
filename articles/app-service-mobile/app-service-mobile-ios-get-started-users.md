<properties
    pageTitle="Azure 行動應用程式在 iOS 上新增驗證"
    description="瞭解如何使用 Azure 行動應用程式驗證使用者透過各種不同的身分識別提供者，包括 AAD、 Google、 Facebook、 Twitter 和 Microsoft iOS 應用程式。"
    services="app-service\mobile"
    documentationCenter="ios"
    authors="ysxu"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-ios-app"></a>新增驗證您的 iOS 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

在本教學課程中，您專案中新增驗證[iOS 快速開始]使用支援的身分識別提供者。 本教學課程中會根據[iOS 快速開始]教學課程中，您必須先完成的。

##<a name="register"></a>註冊您的應用程式的驗證及設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>限制權限已驗證的使用者

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Xcode，按下 [**執行**] 以啟動應用程式。 因為應用程式嘗試存取後端非驗證的使用者，但_TodoItem_表格現在需要驗證，則會引發例外狀況。

##<a name="add-authentication"></a>驗證新增至應用程式

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[iOS 快速入門]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
