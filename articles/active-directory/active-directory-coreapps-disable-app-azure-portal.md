<properties
    pageTitle="停用使用者登增益集的 Azure Active Directory 預覽中企業應用程式 |Microsoft Azure"
    description="如何停用企業應用程式，讓沒有任何使用者可能中登入以便 Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="curtand"/>


# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory-preview"></a>停用 Azure Active Directory 預覽中的企業應用程式的使用者登增益集

您可以輕鬆要停用企業應用程式，以便沒有任何使用者可能中登入以便 Azure Active Directory (Azure AD) 預覽。 [什麼是在預覽中？](active-directory-preview-explainer.md) 您必須有適當的權限，來管理企業應用程式。 在目前的預覽中，您必須是全域管理員的目錄。

## <a name="how-do-i-disable-user-sign-ins"></a>如何停用使用者登增益集？

1. [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2. 選取 [**更多服務**中 [文字] 方塊中，輸入**Azure Active Directory** ，然後按下**enter 鍵**。

3. 在**Azure Active Directory- *directoryname* **刀 （也就是 Azure AD 刀您管理目錄），選取 [**企業應用程式 * *。

    ![開啟企業應用程式](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)

4. 在 [**企業應用程式**刀中，選取 [**所有應用程式**]。 您會看到您可以管理應用程式的清單。

5. 在 [**企業應用程式的所有應用程式**刀中，選取 [應用程式]。

6. 在***應用程式名稱***刀 （也就是在標題中的選取應用程式名稱刀） 中，選取 [**內容**]。

    ![選取 [所有應用程式] 命令](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)

7. 在 [***應用程式名稱*** **-內容**刀選取**不****啟用的使用者登入？**。

8. 選取 [**儲存**] 命令。

## <a name="next-steps"></a>後續步驟

- [請參閱我所有的群組](active-directory-groups-view-azure-portal.md)
- [指定使用者或群組至企業應用程式](active-directory-coreapps-assign-user-azure-portal.md)
- [企業應用程式中移除使用者或群組的工作分派](active-directory-coreapps-remove-assignment-azure-portal.md)
- [變更的名稱或標誌企業應用程式](active-directory-coreapps-change-app-logo-user-azure-portal.md)
