<properties
    pageTitle="將新的使用者新增至 Azure Active Directory |Microsoft Azure"
    description="說明如何新增使用者或變更 Azure Active Directory 中的使用者資訊。"
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
    ms.topic="get-started-article"
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>將新使用者或使用者以 Microsoft 帳戶新增至 Azure Active Directory

新增使用者，填入您的目錄。 本文說明如何新增貴組織中的新使用者，以及如何新增 Microsoft 帳戶的使用者。 如需有關將使用者新增其他 Azure Active Directory 中的目錄，或將使用者新增的合作夥伴的詳細資訊，請參閱[新增使用者，從其他目錄或 Azure Active Directory 中的合作夥伴](active-directory-create-users-external.md)。 新增的使用者不具有管理員權限，根據預設，但您可以隨時為其指派角色。

## <a name="add-a-user"></a>新增使用者

1. [Azure 傳統入口網站](https://manage.windowsazure.com)的目錄是全域管理員帳戶登入。
2. 選取 [ **Active Directory**]，然後選取 [您組織的目錄的名稱。
3. 選取 [**使用者**] 索引標籤，然後在 [命令] 列中，選取 [**新增使用者**。
4. 在 [**告訴我們瞭解此使用者**] 頁面**的使用者類型**] 下選取以下一項︰

    - **您組織中的新使用者**– 在您的目錄新增新的使用者帳戶。
    - **使用現有的 Microsoft 帳戶的使用者**-新增現有的 Microsoft 消費者帳戶至您的目錄 （例如，Outlook 帳戶）

5. 根據**使用者的類型**，請輸入 （適用於新的使用者） 的使用者名稱或電子郵件地址 （適用於使用者以 Microsoft 帳戶）。
6. 在 [使用者**設定檔**] 頁面上提供姓氏和名字、 好記的名稱及使用者角色，從 [**角色**] 清單。 如需關於使用者和管理員角色的詳細資訊，請參閱[指派管理員角色中 Azure AD](active-directory-assign-admin-roles.md)。 指定是否要**啟用多因素驗證**的使用者。
7. 在 [**取得暫時密碼**] 頁面上，選取 [**建立**。

> [AZURE.IMPORTANT] 如果您的組織使用一個以上的網域，您應該瞭解下列問題當您新增使用者帳戶︰
>
> - 若要在網域新增有相同的使用者主要名稱 (UPN) 的使用者帳戶，**第一**新增，例如geoffgrisso@contoso.onmicrosoft.com,**後面** geoffgrisso@contoso.com。
> - **不要**加上geoffgrisso@contoso.com您加入之前geoffgrisso@contoso.onmicrosoft.com。 此順序的重要性，並可以很難復原。

## <a name="change-user-information"></a>變更使用者資訊

您可以變更任何使用者屬性，除了物件識別碼。

1. 開啟您的目錄。
2. 選取 [**使用者**] 索引標籤，然後選取 [您想要變更的使用者的顯示名稱。
3. 完成您的變更，然後再按一下 [**儲存**。

如果您要變更的使用者可以與您內部部署的 Active Directory 服務同步處理，您無法變更使用此程序的使用者資訊。 若要變更的使用者，請使用您的內部部署 Active Directory 管理工具。

## <a name="guest-user-management-and-limitations"></a>來賓使用者管理和限制

來賓帳戶是使用者從其他受邀目錄存取 SharePoint 文件、 應用程式] 或其他 Azure 資源的目錄。 在您的目錄中以來賓帳戶有其基礎的 UserType 屬性設定為 「 來賓 」。 一般使用者 （特別是您的目錄的成員） 有 UserType 屬性 「 成員 」。

訪客的目錄中有一組的權限。 這些權限限制來賓找出目錄中的其他使用者的相關資訊的能力。 不過，來賓使用者仍然可以使用了使用者和群組他們正在處理的資源相關聯。 來賓使用者可以︰

- 請參閱其他使用者和群組已將獲指派的 Azure 訂閱相關聯
- 請參閱所屬的群組的成員
- 查詢在目錄中，其他使用者知道使用者的完整的電子郵件地址
- 請參閱只有有限的查閱，以顯示名稱、 電子郵件地址、 使用者主要名稱 (UPN) 及縮圖相片有限的使用者的屬性
- 取得驗證的網域，在目錄中的清單
- 應用程式]，在您的目錄中的成員有相同的存取權授與他們同意

## <a name="set-guest-user-access-policies"></a>設定來賓使用者存取原則

目錄中的 [**設定**] 索引標籤包括來控制存取權的來賓使用者的選項。 這些選項可以變更僅在 Azure 傳統的入口網站目錄全域管理員。 目前沒有 PowerShell 或 API 的方法。

若要開啟 Azure 傳統入口網站中的 [**設定**] 索引標籤，選取**Active Directory**]，然後選取目錄的名稱。

![Azure Active Directory 中設定] 索引標籤][1]

然後，您可以編輯的選項來控制來賓使用者的存取權。

![來賓使用者存取控制選項][2]


## <a name="whats-next"></a>下一步是什麼

- [將使用者新增其他目錄或 Azure Active Directory 中的合作夥伴](active-directory-create-users-external.md)
- [管理 Azure AD](active-directory-administer.md)
- [Azure AD 中管理密碼](active-directory-manage-passwords.md)
- [Azure AD 中管理群組](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
