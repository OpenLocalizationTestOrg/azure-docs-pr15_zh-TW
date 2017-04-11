<properties
    pageTitle="帳戶佈建通知 |Microsoft Azure"
    description="瞭解如何確保您會收到通知的使用者佈建與相關的問題需要您留意啟用帳戶佈建通知。"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>


# <a name="account-provisioning-notifications"></a>帳戶佈建通知

使用使用者佈建，您可以自動管理使用者在第三方 SaaS 應用程式中的程序。 <br>
雖然這是自動化程序，使用此程序的互動不時需要。 <br>
這是，例如大小寫，當您設定 exchange 資料的第三個帳戶的密碼派對的 SaaS 應用程式已過期。 

啟用帳戶佈建通知，您可以確保您會收到通知的使用者佈建與相關需要注意的問題。

您啟動或停用的帳戶提供通知做為您佈建的協力廠商 SaaS 應用程式設定的使用者的一部分。

![使用者佈建][1] 



若要啟動帳戶佈建通知，選取 [**確認**] 對話方塊] 頁面上的相關核取方塊，然後輸入的收件者的電子郵件別名。

![帳戶佈建通知][2]
 


您可以輸入通訊群組清單為收件者。不過，很重要的通知電子郵件包含只 Azure AD 系統管理員可以存取的報表的連結。

如果您有帳戶佈建啟用的通知，您會收到關於重大問題使用者佈建與相關的電子郵件。 不過，若要避免電子郵件超量的狀況，您只會收到一每日每個 SaaS 應用程式，啟用通知電子郵件的通知電子郵件。


##<a name="related-articles"></a>相關的文章

- [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)
- [自動化使用者佈建/取消提供 SaaS 應用程式](active-directory-saas-app-provisioning.md)
- [自訂使用者提供的屬性對應](active-directory-saas-customizing-attribute-mappings.md)
- [撰寫屬性對應的運算式](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [範圍使用者佈建的篩選](active-directory-saas-scoping-filters.md)
- [若要啟用自動提供 [使用者和群組的 Azure Active Directory 從應用程式使用 SCIM](active-directory-scim-provisioning.md)
- [教學課程如何整合 SaaS 應用程式的清單](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png