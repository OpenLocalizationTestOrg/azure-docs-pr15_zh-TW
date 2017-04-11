<properties
    pageTitle="管理您的群組是 Azure Active Directory 預覽中的成員群組 |Microsoft Azure"
    description="群組可以包含其他 Azure Active Directory 中的群組。 以下說明如何管理這些成員資格。"
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
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="manage-the-groups-your-group-is-a-member-of-in-azure-active-directory-preview"></a>管理您的群組是 Azure Active Directory 預覽中的成員的群組

群組可以包含其他 Azure Active Directory 預覽中的群組。 [什麼是在預覽中？](active-directory-preview-explainer.md) 以下說明如何管理這些成員資格。

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>如何找到我的群組的成員群組？

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取**更多服務**中 [文字] 方塊中，輸入**使用者和群組]** ，然後按下**enter 鍵**。

  ![開啟管理使用者](./media/active-directory-groups-membership-azure-portal/search-user-management.png)

3.  在 [**使用者和群組**刀中，選取 [**所有群組**]。

  ![開啟群組刀](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)

4. 在 [**使用者和群組的所有群組**刀中，選取一個群組。

5. 在**群組的*群組名稱***防禦，以選取**群組成員資格 * *。

  ![開啟群組成員資格刀](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)

6. 若要新增為**群組的成員資格**刀上的另一個群組的成員的群組中，選取 [**新增**] 命令。

7. 從 [**選取群組**刀中，選取一個群組，然後選取 [刀底部的 [**選取**] 按鈕。 您可以在一次只能有一個群組新增您的群組。 [**使用者**] 方塊篩選顯示根據相符的項目到任何部分的使用者或裝置的名稱。 任何萬用字元會不接受] 方塊中。

  ![新增群組成員資格](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)

8. 若要移除群組成員的另一個群組中，在**群組的成員資格**防禦，以選取群組。

9. 在***群組名稱***刀中，選取 [**移除**] 命令，然後確認出現提示時選擇。

  ![移除成員資格] 命令](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)

9. 當您完成變更群組的成員資格時，請選取 [**儲存**]。


## <a name="additional-information"></a>其他資訊

這些文章提供 Azure Active Directory 的其他資訊。

* [請參閱現有的群組](active-directory-groups-view-azure-portal.md)
* [建立新的群組，並新增成員](active-directory-groups-create-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員](active-directory-groups-members-azure-portal.md)
* [管理動態規則的群組中的使用者](active-directory-groups-dynamic-membership-azure-portal.md)
