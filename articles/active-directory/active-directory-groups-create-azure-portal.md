<properties
    pageTitle="Azure Active Directory 預覽中建立新的群組 |Microsoft Azure"
    description="Azure Active Directory 中建立群組，並在群組新增使用者 （成員）"
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


# <a name="create-a-new-group-in-azure-active-directory-preview"></a>Azure Active Directory 預覽中建立新的群組

> [AZURE.SELECTOR]
- [Azure 入口網站](active-directory-groups-create-azure-portal.md)
- [Azure 傳統入口網站](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)

本文說明如何建立和填入新的群組中的 Azure Active Directory (Azure AD) 預覽。 [什麼是在預覽中？](active-directory-preview-explainer.md) 您可以使用 [群組來執行管理工作，例如指派授權] 或 [權限給使用者或裝置的數字，一次。

## <a name="how-do-i-create-a-group"></a>我要如何建立群組？

1. [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2. 選取 [**更多服務**中 [文字] 方塊中，輸入**使用者和群組**，然後按下**enter 鍵**。

  ![開啟管理使用者](./media/active-directory-groups-create-azure-portal/search-user-management.png)

3. 在 [**使用者和群組**刀中，選取 [**所有群組**]。

  ![開啟群組刀](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)

4. 在 [**使用者和群組的所有群組**刀中，選取 [**新增**] 命令。

  ![選取 [新增] 命令](./media/active-directory-groups-create-azure-portal/add-group-command.png)

5. 在**群組**防禦，以新增的名稱和群組的描述。

6. 若要選取要新增到群組的成員，選取**已指派****的成員資格類型**] 方塊中，然後選取**成員**。 如需有關如何動態管理群組的成員資格的詳細資訊，請參閱[使用屬性建立進階的規則的群組成員資格](active-directory-groups-dynamic-membership-azure-portal.md)。

  ![選取要新增成員](./media/active-directory-groups-create-azure-portal/select-members.png)

5. 上**成員**刀中，選取一或多個使用者或裝置新增到群組，然後選取 [刀底部的 [**選取**] 按鈕將它們新增至群組。 [**使用者**] 方塊篩選顯示根據相符的項目到任何部分的使用者或裝置的名稱。 任何萬用字元會不接受] 方塊中。

6. 當您完成新增成員至群組時，請選取 [**群組**刀上的 [**建立**]。    

  ![建立群組確認](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)




## <a name="additional-information"></a>其他資訊

這些文章提供 Azure Active Directory 的其他資訊。

* [請參閱現有的群組](active-directory-groups-view-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員](active-directory-groups-members-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理動態規則的群組中的使用者](active-directory-groups-dynamic-membership-azure-portal.md)
