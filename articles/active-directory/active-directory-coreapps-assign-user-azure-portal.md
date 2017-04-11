<properties
    pageTitle="指定使用者或群組至 Azure Active Directory 預覽中企業應用程式 |Microsoft Azure"
    description="如何選取企業應用程式為其指定使用者或群組 Azure Active Directory 中"
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
    ms.date="10/03/2016"
    ms.author="curtand"/>

# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>指定使用者或群組至 Azure Active Directory 預覽中企業應用程式

您可以輕鬆指派企業應用程式 Azure Active Directory (Azure AD) 預覽中的使用者或群組。 [什麼是在預覽中？](active-directory-preview-explainer.md) 您必須有適當的權限，來管理企業應用程式。 在目前的預覽中，您必須是全域管理員的目錄。

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>如何將使用者存取權限指派給企業應用程式？

1. [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2. 選取 [**更多服務**中 [文字] 方塊中，輸入 Azure Active Directory，然後按下**enter 鍵**。

3. 在**Azure Active Directory- *directoryname* **刀 （也就是 Azure AD 刀您管理目錄），選取 [**企業應用程式 * *。

    ![開啟企業應用程式](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)

4. 在 [**企業應用程式**刀中，選取 [**所有應用程式**]。 您會看到您可以管理應用程式的清單。

5. 在 [**企業應用程式的所有應用程式**刀中，選取 [應用程式]。

6. 在***應用程式名稱***刀 （也就是在標題中的選取應用程式名稱刀） 中，選取 [**使用者和群組]**。

    ![選取 [所有應用程式] 命令](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)

7. 在 [***應用程式名稱*** **-使用者及群組指派**刀，選取 [**新增**] 命令。

8. 在 [**新增工作分派**刀中，選取**使用者和群組]**。

    ![指定使用者或群組至應用程式](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)

9. 在**使用者和群組**刀中，從清單中選取一或多個使用者或群組，然後選取 [刀底部的 [**選取**] 按鈕。

10. 在 [**新增工作分派**刀中，選取 [**角色**]。 然後在 [**選取角色**刀中，選取 [角色即可套用至所選的使用者或群組]，然後按一下刀底部的 [**確定**] 按鈕。

11. 在**新增的工作分派**刀中，選取刀底部的 [**指派**] 按鈕。 指派的使用者或群組有定義此企業應用程式的所選角色的權限。

## <a name="next-steps"></a>後續步驟

- [請參閱所有我的群組](active-directory-groups-view-azure-portal.md)
- [企業應用程式中移除使用者或群組的工作分派](active-directory-coreapps-remove-assignment-azure-portal.md)
- [停用企業應用程式的使用者登增益集](active-directory-coreapps-disable-app-azure-portal.md)
- [變更的名稱或標誌企業應用程式](active-directory-coreapps-change-app-logo-user-azure-portal.md)
