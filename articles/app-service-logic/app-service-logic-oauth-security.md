<properties
    pageTitle="OAUTH 安全性 SaaS 連接器與 API 應用程式 |Azure"
    description="閱讀關於 OAUTH 連接器和 Azure 應用程式服務; API office 應用程式中的安全性microservices 架構。saas"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>瞭解 OAUTH 證券中 SaaS 連接器

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽結構描述版本。

許多為等 Facebook、 Twitter 或 DropBox 等需要驗證使用 OAUTH 通訊協定使用者服務 (SaaS) 連接器的軟體。  當您使用邏輯應用程式的這些 SaaS 連接器時，我們會提供更精簡的使用者體驗，邏輯應用程式設計工具] 中按一下 [授權]。 當您**授權**，會要求您登入 （如果沒有已），並提供同意連線至您代表 SaaS 服務。 您提供同意和授權之後，您的邏輯應用程式可以存取這些 SaaS 服務。

## <a name="create-your-own-saas-app"></a>建立您自己的 SaaS 應用程式
此簡化的體驗可能是因為我們先前建立並在 [這些 SaaS 服務中登錄應用程式。  在某些情況下，您可能會想要註冊，並使用您自己的應用程式。  例如，當您想要在您自訂的應用程式中使用這些 SaaS 連接器時，這是有必要，請。 此範例使用 DropBox 連接器，但程序也適用於所有 OAUTH 所依賴的連接器。

即使是在邏輯應用程式的內容，您可以使用自己的應用程式，而不是使用我們所提供的預設應用程式。 如果 [授權] 按鈕無法連線，您可以嘗試建立您自己的應用程式。 以下列出適用於 Twitter 連接器的步驟︰

1. Azure 預覽入口網站中，開啟您 Twitter 的連接器。 移至 [**瀏覽** > **API 應用程式**。 選取您 Twitter 的連接器︰  
    ![][1]

2. 選取 [**設定** > **驗證**︰  
    ![][2]

3. 複製**重新導向 URI**值︰  
    ![][3]

4. 移至[Twitter](http://apps.twitter.com)和**建立新的應用程式**。 在 [**回撥 URL** ] 屬性中，貼上複製您 Twitter 連接器**重新導向 URI**值︰ ![][4]  
5. 建立您的 Twitter 應用程式時，選取 [**索引鍵和存取權杖**]。 複製下列值。
6. 在您 Twitter 連接器驗證設定] 中，貼上在**用戶端識別碼**] 和 [**用戶端私人**屬性的下列值︰   
    ![][5]  
7. 儲存您的連接器設定。  

現在，您應該可以使用您從邏輯應用程式的連接器。 當您使用此連接器從邏輯應用程式時，它會使用您的應用程式，而不是預設的應用程式。  

> [AZURE.NOTE] 如果您先前已授權應用程式，您可能必須重新授權應用程式。


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png
