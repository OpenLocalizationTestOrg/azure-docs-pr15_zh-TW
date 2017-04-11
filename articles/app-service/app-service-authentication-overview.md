<properties
    pageTitle="驗證和 Azure 應用程式服務中的授權 |Microsoft Azure"
    description="概念性參考及驗證概觀 / 授權 Azure 應用程式服務的功能"
    services="app-service"
    documentationCenter=""
    authors="mattchenderson"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="mahender"/>

# <a name="authentication-and-authorization-in-azure-app-service"></a>驗證和 Azure 應用程式服務中的授權

## <a name="what-is-app-service-authentication--authorization"></a>什麼是應用程式服務驗證 / 授權？

應用程式服務驗證 / 授權是一種功能，可讓您的應用程式登入的使用者，好讓您能夠變更應用程式後端上的程式碼。 會提供簡單的方法來保護您的應用程式，並使用的每位使用者資料。

應用程式服務使用同盟身分識別，協力廠商身分識別提供者儲存帳戶以及驗證使用者。 應用程式依賴提供者的身分識別的資訊，讓應用程式沒有儲存本身的資訊。 應用程式服務支援現成的五個身分識別提供者︰ Azure Active Directory、 Facebook、 Google、 Microsoft 帳戶和 Twitter。 您的應用程式可以使用這些身分識別提供者的任何數字，以提供您的使用者選項的使用者登入的方式。 若要展開的內建的支援，您可以整合其他身分識別提供者或[自己自訂的身分識別的解決方案][custom-auth]。

如果您想要立即開始使用，請參閱下列教學課程其中一項︰

- [新增驗證您的 iOS 應用程式][ iOS] （或[Android]、 [Windows]、 [Xamarin.iOS]、 [Xamarin.Android]、 [Xamarin.Forms]或[Cordova]）
- [使用者驗證的 API Azure 應用程式服務中的應用程式][apia-user]
- [開始使用 Azure 應用程式服務-第 2 部分][web-getstarted]

## <a name="how-authentication-works-in-app-service"></a>驗證應用程式服務中的運作方式

若要使用其中一個身分識別提供者驗證，您必須設定身分識別提供者，若要知道您的應用程式。 識別碼及應用程式服務所提供的密碼，然後將提供身分識別提供者。 讓應用程式服務可以驗證使用者判斷提示，例如驗證的權杖，從身分識別提供者已完成的信任關係。

若要登入使用者使用其中一個提供者，使用者必須重新導向到端點的提供者在使用者登入。 如果客戶使用網頁瀏覽器，您可以讓應用程式服務會自動將所有未授權的使用者登入使用者的端點。 否則，您需要的客戶`{your App Service base URL}/.auth/login/<provider>`，其中`<provider>`是其中一個下列的值︰ aad，facebook google，microsoft 或 twitter。 行動電話和 API 分析藍本本文稍後的節的說明。

透過網頁瀏覽器的應用程式與之互動的使用者會有 cookie 設定，好讓他們可以保持經過驗證瀏覽您的應用程式。 對於其他用戶端類型，如行動電話，JSON web 權杖 (JWT)，其中應該可呈現`X-ZUMO-AUTH`標頭，將會發出用戶端。 行動應用程式用戶端 Sdk 會為您處理此。 或者，在 Azure Active Directory 身分識別權杖或存取權杖可能會直接包含在`Authorization`作為[承載者權杖](https://tools.ietf.org/html/rfc6750)的標題。

應用程式服務會驗證任何 cookie 或您的應用程式發生的問題來驗證使用者的 token。 若要限制誰能夠存取您的應用程式，請參閱本文稍後的[授權](#authorization)一節。

### <a name="mobile-authentication-with-a-provider-sdk"></a>提供者 SDK 行動驗證

所有項目設定後端上之後，您可以修改行動用戶端登入應用程式服務。 有以下兩種方法︰

- 使用指定的身分識別提供者發佈建立身分識別，然後存取應用程式服務 SDK。
- 使用一行程式碼，以便在行動應用程式的用戶端 SDK 可以登入的使用者。

>[AZURE.TIP] 大部分的應用程式應該使用 SDK 「 提供者以取得更多一致的經驗，當使用者登入，若要使用重新整理支援，並取得其他提供者指定的優點。

當您使用 SDK 「 提供者時，使用者可以登入體驗，更緊密地整合作業系統上執行應用程式。 這也會提供您的提供者權杖和使用者資訊在用戶端，可讓您更容易使用圖形 Api 和自訂使用者經驗。 有時候部落格和論壇，您會看到這稱為 「 用戶端流量 」 或 「 用戶端導向流程 「 用戶端上的程式碼登入使用者]，然後用戶端程式碼，因為有權存取的提供者權杖。

取得的提供者權杖之後，需要驗證傳送到應用程式服務。 應用程式服務驗證的權杖之後，應用程式服務會建立新的應用程式服務權杖，傳回用戶端。 行動應用程式用戶端 SDK 具有協助管理此 exchange，並自動將權杖附加至應用程式後端的所有要求的方法。 如果他們，因此請選擇開發人員也可以隨時提供者權杖的參考。

### <a name="mobile-authentication-without-a-provider-sdk"></a>不提供者 SDK 的行動驗證

如果您不想設定 SDK 「 提供者，您可以讓 Azure 應用程式服務登入您的行動應用程式功能。 在行動應用程式的用戶端 SDK 會開啟至您所選擇的提供者網頁檢視，並登入使用者。 有時候部落格和論壇，您會看到這稱為 「 伺服器流程 」 或 「 伺服器導向流程 「 因為伺服器管理中的使用者，請登入程序，用戶端 SDK 永遠不會收到的提供者權杖。

若要開始此流程的程式碼會包含在驗證教學課程中的每個平台。 流程結尾的用戶端 SDK 有應用程式服務權杖，而且權杖會自動附加至應用程式後端的所有要求。

### <a name="service-to-service-authentication"></a>服務-服務驗證

雖然您可以授權讓使用者存取您的應用程式，您也可以信任撥打您自己的 API 的另一個應用程式。 例如，您可以讓來電 API 其他 web 應用程式中的其中一個 web 應用程式。 在此案例中，您使用的認證，而不是使用者認證服務帳戶存取權杖。 指定帳戶給服務也稱為*服務本金*Azure Active Directory 說法，而使用這類帳戶的驗證會也稱為服務-案例。

>[AZURE.IMPORTANT] 因為客戶裝置上執行的行動應用程式，行動應用程式執行_不_為受信任應用程式]，然後不應使用的服務主要流程的計數。 不過，他們應該使用流量已詳細的較舊版本的使用者。

服務的情況下，應用程式服務可以使用 Azure Active Directory 保護您的應用程式。 呼叫的應用程式只需要提供取得提供用戶端識別碼和密碼的 Azure Active Directory 的用戶端的 Azure Active Directory 服務本金授權權杖。 ASP.NET API 應用程式會使用這種情況的範例會說明的教學課程中，[服務 API 應用程式的本金驗證][apia-service]。

如果您想要使用應用程式服務驗證來處理服務-服務情況，您可以使用用戶端憑證] 或 [基本驗證。 Azure 中的用戶端憑證的相關資訊，請參閱[如何以設定 TLS 相互驗證的 Web 應用程式](../app-service-web/app-service-web-configure-tls-mutual-auth.md)。 ASP.NET 中的基本驗證的相關資訊，請參閱[驗證 ASP.NET Web API 2] 中的篩選](http://www.asp.net/web-api/overview/security/authentication-filters)。

從應用程式服務邏輯應用程式至 API 應用程式的服務帳戶驗證是一種特殊情況的詳細說明[的使用您自訂的 API 裝載於邏輯應用程式的應用程式服務](../app-service-logic/app-service-logic-custom-hosted-api.md)。

## <a name="authorization"></a>授權服務應用程式中的運作方式

您必須能夠存取您的應用程式要求的完全控制權。 應用程式服務驗證 / 授權可以設定的任何下列問題︰

- 允許已驗證的要求僅連絡您的應用程式。

    在瀏覽器收到匿名要求時，如果應用程式服務會重新導向至頁面您選擇讓使用者可以登入身分識別提供者。 如果要求從行動裝置，傳回的回應是 HTTP _401 未經授權_的回應。

    使用此選項，您不需要在撰寫應用程式中的任何驗證碼。 如果您需要更妥善地授權，就有一個使用者的相關資訊位於您的程式碼。

- 允許所有邀請達到您的應用程式，但驗證經過驗證的要求，並傳遞 HTTP 標頭中的驗證資訊。

    這個選項會將您的應用程式碼的授權決策延後。 會提供更具彈性處理匿名要求，但您必須撰寫程式碼。

- 讓所有邀請達到您的應用程式，並在邀請中的驗證資訊採取任何動作。

    在這種情況下驗證 / 授權功能關閉。 驗證與授權的工作是完全取決於您的應用程式碼。

Azure 入口網站中的 [**要求未經驗證時採取的動作**] 選項會控制先前的行為。 如果您選擇 [**登入*提供者名稱* **，必須先通過驗證所有要求。**允許要求 （沒有巨集指令） * * 延後授權決定您的程式碼，不過它仍然會提供驗證資訊。 如果您想要處理所有項目程式碼，您可以停用驗證 / 授權功能。

## <a name="working-with-user-identities-in-your-application"></a>使用應用程式中的使用者身分識別

應用程式服務會使用特殊的標題傳遞給您的應用程式的一些使用者資訊。 外部要求禁止這些標題，然後才會出現 if 設定應用程式服務驗證 / 授權。 部分範例標題包括︰

* X-MS-用戶端-主要-名稱
* X-MS-用戶端-本金-識別碼
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

所撰寫的任何語言或架構的程式碼可以從下列標題取得所需的資訊。 ASP.NET 4.6 應用程式，系統會自動設定 [ **ClaimsPrincipal**以適當的值。

您的應用程式上也可以取得其他使用者的詳細資料，透過 HTTP GET`/.auth/me`應用程式的結束點。 包含要求的有效權杖會傳回 JSON 裝載提供者所使用的相關詳細資料、 基礎的提供者權杖，與其他的使用者資訊。 行動應用程式伺服器 Sdk 提供協助處理此資料的方法。 如需詳細資訊，請參閱[如何使用 Azure Mobile 應用程式 Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)，並[使用.NET 後端伺服器 SDK Azure Mobile 應用程式](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)。

## <a name="documentation-and-additional-resources"></a>文件及其他資源

### <a name="identity-providers"></a>身分識別提供者
下列教學課程顯示如何設定應用程式服務，以便使用不同的驗證提供者︰

- [如何設定您的應用程式使用 Azure Active Directory 登入][AAD]
- [如何設定您的應用程式使用 Facebook 登入][Facebook]
- [如何設定您的應用程式使用 Google 登入][Google]
- [如何設定您的應用程式使用 Microsoft 帳戶登入][MSA]
- [如何設定您的應用程式使用 Twitter 登入][Twitter]

如果您要使用此處所未提供身分識別系統時，您也可以使用的[預覽自訂驗證支援在行動應用程式.NET server SDK][custom-auth]，可以使用 web 應用程式、 行動應用程式或 API 應用程式中。

### <a name="web-applications"></a>Web 應用程式
下列教學課程顯示如何新增驗證的 web 應用程式︰

- [開始使用 Azure 應用程式服務-第 2 部分][web-getstarted]

### <a name="mobile-applications"></a>行動應用程式
下列教學課程顯示如何新增至您的行動用戶端的驗證，使用伺服器導向流程︰

- [新增驗證您的 iOS 應用程式][iOS]
- [新增驗證您的 Android 應用程式][android]
- [新增至您的 Windows 應用程式的驗證][Windows]
- [新增驗證 Xamarin.iOS 應用程式][Xamarin.iOS]
- [新增驗證 Xamarin.Android 應用程式][Xamarin.Android]
- [新增驗證 Xamarin.Forms 應用程式][Xamarin.Forms]
- [新增驗證 Cordova 應用程式][Cordova]

如果您想要用於 Azure Active Directory 中的用戶端導向流程，請使用下列資源︰

- [IOS 版使用 Active Directory 驗證庫][ADAL-iOS]
- [Android 版使用 Active Directory 驗證文件庫][ADAL-Android]
- [Windows 和 Xamarin 使用 Active Directory 驗證文件庫][ADAL-dotnet]

如果您想要使用的用戶端導向流程 facebook，請使用下列資源︰

- [使用 iOS 版的 Facebook SDK](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

如果您想要使用 Twitter 用戶端導向流程，請使用下列資源︰

- [IOS 版中使用 Twitter 布料的轉印圖樣](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

如果您想要用於 Google 的用戶端導向流程，請使用下列資源︰

- [使用 Google 登入 SDK iOS 版](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

### <a name="api-applications"></a>API 應用程式
下列教學課程顯示如何保護您的 API 應用程式︰

- [使用者驗證的 API Azure 應用程式服務中的應用程式][apia-user]
- [Azure 應用程式服務中的 API 應用程式服務本金驗證][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[web-getstarted]: ../app-service-web/app-service-web-get-started-2.md#authenticate-your-users

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
