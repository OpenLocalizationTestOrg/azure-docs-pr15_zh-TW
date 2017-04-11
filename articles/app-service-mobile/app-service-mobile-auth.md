<properties
    pageTitle="驗證和 Azure 行動應用程式中的授權 |Microsoft Azure"
    description="概念性參考及驗證概觀 / 授權 Azure Mobile 應用程式的功能"
    services="app-service\mobile"
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

# <a name="authentication-and-authorization-in-azure-mobile-apps"></a>驗證和 Azure 行動應用程式中的授權

## <a name="what-is-app-service-authentication--authorization"></a>什麼是應用程式服務驗證 / 授權？

> [AZURE.NOTE] 本主題將也會移轉至合併[應用程式服務驗證 / 授權](../app-service/app-service-authentication-overview.md)主題說明網頁、 行動電話，以及 API 應用程式。

應用程式服務驗證 / 授權是功能可讓您的應用程式，以所需的應用程式後端無程式碼變更使用者登入。 會提供簡單的方法來保護您的應用程式，並使用的每位使用者資料。

應用程式服務使用同盟身分識別，3rd 廠商**身分識別提供者**(「 IDP 」) 會儲存帳戶和驗證使用者，而且應用程式，而不是自己使用此身分識別。 應用程式服務支援現成的五個身分識別提供者︰ _Azure Active Directory_、 _Facebook_、 _Google_、 _Microsoft 帳戶_和_Twitter_。 您也可以整合另一個身分識別提供者或自己自訂的身分識別的解決方案，您的應用程式的展開此支援。

您的應用程式可以利用這些身分識別提供者，任何數字，可讓使用者以他們的登入的選項。

如果您想要立即開始使用，請參閱下列教學課程其中一項︰

- [新增驗證您的 iOS 應用程式]
- [新增驗證 Xamarin.iOS 應用程式]
- [新增驗證 Xamarin.Android 應用程式]
- [新增驗證您的 Windows 應用程式]

## <a name="how-authentication-works"></a>驗證的運作方式

若要使用其中一個身分識別提供者，進行驗證，您必須設定身分識別提供者，若要知道您的應用程式。 身分識別提供者然後會提供您識別碼與回應用程式所提供的密碼。 這會完成的信任關係，並允許以驗證身分識別提供給該應用程式服務。

這些步驟所述下列主題︰

- [如何設定您的應用程式使用 Azure Active Directory 登入]
- [如何設定您的應用程式使用 Facebook 登入]
- [如何設定您的應用程式使用 Google 登入]
- [如何設定您的應用程式使用 Microsoft 帳戶登入]
- [如何設定您的應用程式使用 Twitter 登入]

一旦在後端上設定所有項目，您可以修改您的用戶端登入。 有以下兩種方法︰

- 使用程式碼，讓用戶端 SDK 登入使用者的行動應用程式。
- 運用 SDK 發佈指定身分識別提供者建立身分識別，然後存取應用程式服務。

>[AZURE.TIP] 大部分的應用程式應該使用 SDK 「 提供者，以取得更多的原生覺得登入體驗，以及運用重新整理支援及其他提供者特有的優點。

### <a name="how-authentication-without-a-provider-sdk-works"></a>沒有 SDK 「 提供者驗證的運作方式

如果您不想設定 SDK 「 提供者，您就可以執行登入您的行動應用程式。 行動應用程式用戶端 SDK 會開啟至您所選擇的提供者網頁檢視，並完成登入。 有時候在部落格和論壇，您會看到此參照為 「 伺服器流程 」 或 「 伺服器導向流程 「 自伺服器由管理登入，並在用戶端 SDK 永遠不會收到的提供者權杖。

驗證教學課程中的每個平台討論此流程所需的程式碼。 流程結尾的用戶端 SDK 有應用程式服務權杖，而且權杖會自動附加至後端的所有要求。

### <a name="how-authentication-with-a-provider-sdk-works"></a>驗證提供者 SDK 的運作方式

使用提供者 SDK 可讓登入體驗更緊密互動平台作業系統執行應用程式。 這也會提供您的提供者權杖和使用者資訊在用戶端，可讓您更容易使用圖形 Api 和自訂使用者經驗。 有時候部落格和論壇，您會看到這稱為 「 用戶端流量 」 或 「 用戶端導向流程 「 自程式碼在用戶端正在處理的登入，與用戶端程式碼有權存取的提供者權杖。

一旦取得的提供者權杖時，需要驗證傳送到應用程式服務。 流程結束時，在用戶端 SDK 具備應用程式服務權杖，並且權杖會自動附加至後端的所有要求。 如果他們，因此請選擇開發人員也可以隨時提供者權杖的參考。

## <a name="how-authorization-works"></a>授權的運作方式

應用程式服務驗證 / 授權公開幾項選擇**要求未經驗證時採取**的動作。 您的程式碼會收到指定的要求之前，您可以安裝應用程式服務檢查如果驗證要求和 if not、 拒絕並嘗試將有之前再試一次登入的使用者。

其中一個選項是有未經驗證的要求重新導向至其中一個身分識別提供者。 網頁瀏覽器中，這確實想需要使用者至新頁面。 不過，如此一來，您的行動用戶端無法重新導向，未經認證的回覆會收到 HTTP _401 未經授權_的回應。 如此一來，您的客戶可讓您的第一個要求登入端點，一律採用，然後使用任何其他 api 打電話。 如果您嘗試登入之前撥打另一個 API，您的用戶端會收到錯誤。

如果您想要提供更細緻控制的端點上需要驗證，您也可以挑選 」 執行任何動作 （允許邀請） 」 的未經驗證的要求。 在此情況下，所有驗證決策會延都後，您的應用程式碼。 這也可讓您允許根據自訂授權規則的特定使用者的存取權。

## <a name="documentation"></a>文件

下列教學課程顯示如何將驗證新增至行動用戶端使用應用程式服務︰

- [新增驗證您的 iOS 應用程式]
- [新增驗證 Xamarin.iOS 應用程式]
- [新增驗證 Xamarin.Android 應用程式]
- [新增驗證您的 Windows 應用程式]

下列教學課程顯示如何設定應用程式服務運用不同的驗證提供者︰

- [如何設定您的應用程式使用 Azure Active Directory 登入]
- [如何設定您的應用程式使用 Facebook 登入]
- [如何設定您的應用程式使用 Google 登入]
- [如何設定您的應用程式使用 Microsoft 帳戶登入]
- [如何設定您的應用程式使用 Twitter 登入]

如果您使用此處所未提供身分識別系統，您也可以充分利用[預覽自訂驗證支援.NET server SDK 中的](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth)。

[新增驗證您的 iOS 應用程式]: app-service-mobile-ios-get-started-users.md
[新增驗證 Xamarin.iOS 應用程式]: app-service-mobile-xamarin-ios-get-started-users.md
[新增驗證 Xamarin.Android 應用程式]: app-service-mobile-xamarin-android-get-started-users.md
[新增驗證您的 Windows 應用程式]: app-service-mobile-windows-store-dotnet-get-started-users.md

[如何設定您的應用程式使用 Azure Active Directory 登入]: app-service-mobile-how-to-configure-active-directory-authentication.md
[如何設定您的應用程式使用 Facebook 登入]: app-service-mobile-how-to-configure-facebook-authentication.md
[如何設定您的應用程式使用 Google 登入]: app-service-mobile-how-to-configure-google-authentication.md
[如何設定您的應用程式使用 Microsoft 帳戶登入]: app-service-mobile-how-to-configure-microsoft-authentication.md
[如何設定您的應用程式使用 Twitter 登入]: app-service-mobile-how-to-configure-twitter-authentication.md
