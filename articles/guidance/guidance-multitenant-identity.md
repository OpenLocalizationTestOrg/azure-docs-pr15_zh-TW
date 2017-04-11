<properties
   pageTitle="身分識別管理 Multitenant 應用程式 |Microsoft Azure"
   description="Multitenant 應用程式中的驗證、 授權及身分識別管理的最佳作法。"
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
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Microsoft Azure 中 multitenant 應用程式的身分識別管理

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本系列在使用 Azure AD 驗證與身分識別管理時，會說明 multitenancy 的最佳作法。

當您建立的 multitenant 應用程式時，第一個的挑戰管理使用者身分識別，因為每位使用者現在屬於租用戶。 例如︰

- 使用組織認證，登入使用者。
- 使用者應該存取組織的資料，但不是屬於其他租用戶中的資料。
- 組織可以登入應用程式，並再將應用程式角色指派給其成員。

Azure Active Directory (Azure AD) 有一些很棒的功能，支援所有這些案例。

若要隨著這一系列的文章，因此我們也建立完成的[端對端實作][ tailspin] multitenant 應用程式。 文件，會反映我們學會程序建立的應用程式。 若要開始使用應用程式，請參閱[執行問卷應用程式](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md)。

以下是關於此系列文章的清單︰

- [Multitenant 應用程式的身分識別管理簡介](guidance-multitenant-identity-intro.md)
- [關於 Tailspin 問卷應用程式](guidance-multitenant-identity-tailspin.md)
- [使用 Azure AD 驗證並 OpenID 連線](guidance-multitenant-identity-authenticate.md)
- [使用宣告式身分識別](guidance-multitenant-identity-claims.md)
- [註冊和租用戶登入](guidance-multitenant-identity-signup.md)
- [應用程式角色](guidance-multitenant-identity-app-roles.md)
- [角色和資源為基礎的授權](guidance-multitenant-identity-authorize.md)
- [安全的後端 web API](guidance-multitenant-identity-web-api.md)
- [快取 OAuth2 存取權杖](guidance-multitenant-identity-token-cache.md)
- [客戶 AD FS multitenant Azure 中的應用程式與聯盟](guidance-multitenant-identity-adfs.md)
- [若要從 Azure AD 取得存取權杖使用用戶端判斷提示](guidance-multitenant-identity-client-assertion.md)
- [使用金鑰保存庫保護應用程式的機密資訊](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
