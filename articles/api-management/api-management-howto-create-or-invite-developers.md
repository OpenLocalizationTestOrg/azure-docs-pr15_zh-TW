<properties 
    pageTitle="如何管理 Azure API 管理中的使用者帳戶 |Microsoft Azure" 
    description="瞭解如何建立或邀請 Azure API 管理使用者" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>如何管理 Azure API 管理中的使用者帳戶

API 管理開發人員是您公開使用 API 管理的 Api 的使用者。 若要使用的 Api 及產品本指南會顯示如何建立和邀請開發人員至您進行，並可使用您的 API 管理執行個體。 管理使用者帳戶以程式設計方式的詳細資訊，請參閱[API 管理其餘](https://msdn.microsoft.com/library/azure/dn776326.aspx)參照中的[使用者實體](https://msdn.microsoft.com/library/azure/dn776330.aspx)文件。

## <a name="create-developer"></a>建立新的開發人員

若要建立新的開發人員，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 會帶您到 API 管理 publisher 入口網站。 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

![Publisher 入口網站][api-management-management-console]

從**API 管理**左側功能表上，按一下 [**使用者]** ，然後按一下 [**新增使用者**。

![建立 [開發人員][api-management-create-developer]

輸入新的開發人員的**電子郵件**、**密碼**和**名稱**，然後按一下 [**儲存**]。

![建立 [開發人員][api-management-add-new-user]

根據預設，新建立的開發人員帳戶**作用中**，而 [**開發人員**] 群組相關聯。

![新的開發人員][api-management-new-developer]

若要存取所有的 Api 他們擁有的訂閱可在**作用中**狀態的開發人員帳戶。 若要建立新建立的開發人員與其他群組之間的關聯，請參閱[如何建立與開發人員群組之間的關聯][]。

## <a name="invite-developer"></a>邀請開發人員

若要邀請開發人員，從**API 管理**左側功能表上，按一下 [**使用者]** ，然後再按 [**邀請使用者**。

![邀請開發人員][api-management-invite-developer]

輸入名稱和電子郵件地址的開發人員]，然後按一下 [**邀請**]。

![邀請開發人員][api-management-invite-developer-window]

確認訊息會顯示，但新受邀的開發人員不會出現在清單中，直到待對方接受邀請之後。 

![邀請確認][api-management-invite-developer-confirmation]

當開發人員受邀時，電子郵件會傳送給開發人員。 這封電子郵件會產生使用範本，且可自訂。 如需詳細資訊，請參閱[設定電子郵件範本][]。

後接受邀請，則該帳戶會變成作用中。

## <a name="block-developer"></a>停用或重新啟動開發人員帳戶

根據預設，新建立或受邀的開發人員帳戶都是**作用中**。 若要停用 [開發人員帳戶，請按一下 [**封鎖**]。 若要重新啟動封鎖開發人員帳戶，請按一下 [**啟動**]。 封鎖的開發人員帳戶不可以存取開發人員入口網站，或撥打任何 Api。 若要刪除的使用者帳戶，請按一下 [**刪除**]。

![封鎖開發人員][api-management-new-developer]

## <a name="reset-a-user-password"></a>重設使用者的密碼

若要重設使用者帳戶的密碼，請按一下 [帳戶名稱。

![重設密碼][api-management-view-developer]

按一下 [傳送連結給使用者，重設使用者的密碼的 [**重設密碼**。

![重設密碼][api-management-reset-password]

若要以程式設計方式處理使用者帳戶，請參閱[API 管理其餘](https://msdn.microsoft.com/library/azure/dn776326.aspx)參照中的[使用者實體](https://msdn.microsoft.com/library/azure/dn776330.aspx)文件。 為特定的值，重設使用者的帳戶密碼，您可以使用[更新使用者](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser)的作業，並指定所要的密碼。

## <a name="pending-verification"></a>擱置驗證

![擱置驗證][api-management-pending-verification]

## <a name="next-steps"></a>接下來的步驟

開發人員帳戶建立後，您可以關聯角色和訂閱其產品和 Api。 如需詳細資訊，請參閱[建立及使用群組的方式][]。


[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png
[]: ./media/api-management-howto-create-or-invite-developers/.png



[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[如何建立及使用群組]: api-management-howto-create-groups.md
[如何建立群組與開發人員之間的關聯]: api-management-howto-create-groups.md#associate-group-developer

[開始使用 Azure API 管理]: api-management-get-started.md
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[設定電子郵件範本]: api-management-howto-configure-notifications.md#email-templates