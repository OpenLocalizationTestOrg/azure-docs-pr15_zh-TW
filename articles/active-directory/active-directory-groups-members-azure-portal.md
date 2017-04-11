<properties
    pageTitle="管理 Azure Active Directory 預覽中群組的成員 |Microsoft Azure"
    description="如何將使用者和裝置的 Azure Active Directory 中群組的成員"
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


# <a name="manage-the-members-for-a-group-in-azure-active-directory-preview"></a>管理 Azure Active Directory 預覽中群組的成員

本文說明如何管理 Azure Active Directory (Azure AD) 預覽中群組的成員。 [什麼是在預覽中？](active-directory-preview-explainer.md)

## <a name="how-do-i-find-the-members-and-manage-them"></a>如何尋找成員及進行管理？

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取**更多服務**中 [文字] 方塊中，輸入**使用者和群組]** ，然後按下**enter 鍵**。

  ![開啟管理使用者](./media/active-directory-groups-members-azure-portal/search-user-management.png)

3.  在 [**使用者和群組**刀中，選取 [**所有群組**]。

  ![開啟群組刀](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)

4. 在 [**使用者和群組的所有群組**刀中，選取一個群組。

5. 在**群組的*群組名稱***刀，選取**成員 * *。

  ![開啟成員刀](./media/active-directory-groups-members-azure-portal/view-group-members.png)

6. 若要新增成員至群組]，在**群組的成員**刀中，選取 [**新增成員**]。

  ![新增成員] 命令](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)

7. 上**成員**刀中，選取一或多個使用者或裝置新增到群組，然後選取 [刀底部的 [**選取**] 按鈕將它們新增至群組。 [**使用者**] 方塊篩選顯示根據相符的項目到任何部分的使用者或裝置的名稱。 任何萬用字元會不接受] 方塊中。

8. 若要移除成員，從群組中，在**群組的成員**刀中，選取 [成員]。

9. 在***成員名稱***刀中，選取 [**移除**] 命令，並確認出現提示時選擇。

  ![移除成員] 命令](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)

9. 當您完成變更群組的成員時，請選取 [**儲存**]。


## <a name="additional-information"></a>其他資訊

這些文章提供 Azure Active Directory 的其他資訊。

* [請參閱現有的群組](active-directory-groups-view-azure-portal.md)
* [建立新的群組，並新增成員](active-directory-groups-create-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理動態規則的群組中的使用者](active-directory-groups-dynamic-membership-azure-portal.md)
