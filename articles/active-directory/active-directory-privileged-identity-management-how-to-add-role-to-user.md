<properties
   pageTitle="如何新增或移除使用者的角色 |Microsoft Azure"
   description="瞭解如何新增角色權限的身分識別與 Azure Active Directory 獲授限身分識別管理應用程式。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/24/2016"
   ms.author="kgremban"/>

# <a name="azure-ad-privileged-identity-management-how-to-add-or-remove-a-user-role"></a>Azure 的 AD 有權限的身分識別管理︰ 如何新增或移除使用者的角色

與 Azure Active Directory (AD)，全域管理員 （或公司系統管理員） 可以更新您的使用者會**永久**Azure AD 中指定給角色。 這是使用 PowerShell cmdlet，例如`Add-MsolRoleMember`和`Remove-MsolRoleMember`。 或者，他們可以使用 Azure 傳統入口網站如[指派管理員角色 Azure Active Directory 中的](active-directory-assign-admin-roles.md)所述。

Azure AD 有權限的身分識別管理應用程式可讓特殊權限的角色管理員進行永久性的角色指派，以及。 此外，有權限的角色系統管理員可以讓使用者**符合資格**的管理員角色。 符合資格的管理員可以在需要時，然後再其權限到期後完成時啟動角色。

## <a name="manage-roles-with-pim-in-the-azure-portal"></a>管理與 PIM Azure 入口網站中的角色

在您組織中，您可以指定不同的系統管理角色使用者 Azure AD，Office 365]，及其他 Microsoft 服務及應用程式中。  更多詳細資料的可用角色位於[Azure AD PIM 中的角色](active-directory-privileged-identity-management-roles.md)。

若要新增或移除中使用有權限的身分識別管理角色的使用者，顯示 [PIM 儀表板。 然後按一下 [**管理員角色中的使用者**] 按鈕，或是從角色] 資料表中選取特定的角色 （例如全域管理員）。

> [AZURE.NOTE] 如果您還沒有尚未啟用 PIM Azure 入口網站中，移至[開始使用 Azure AD PIM](active-directory-privileged-identity-management-getting-started.md)如需詳細資訊。

如果您想要授與其他的使用者存取權 PIM 本身，會 PIM 要求使用者擁有的角色是進一步說明如何[授與存取權 PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。

## <a name="add-a-user-to-a-role"></a>將使用者新增至角色

1. 在[Azure 入口網站](https://portal.azure.com/)中，選取儀表板上的 [ **Azure AD 有權限的身分識別管理**] 方塊。
2. 選取 [**管理角色的權限**]。
3. 在 [**角色摘要**] 資料表中，選取您想要管理的角色。
4. 在角色刀中，選取 [**新增**。
5. 按一下 [**選取使用者**，然後搜尋上**選取 [使用者**刀的使用者]。  
6. 從搜尋結果清單中，選取使用者，然後按一下 [**完成**]。
4. 按一下**[確定**] 以儲存您的選取範圍。 您所選取的使用者會出現在清單中，以符合資格的角色。

> [AZURE.NOTE]
>角色中的新使用者才有資格申請預設角色。 如果您想要永久角色，請按一下 [在清單中的使用者]。 使用者的資訊會出現在新的刀。 在 [使用者資訊] 功能表中，選取 [**讓權限**]。  
>如果使用者無法註冊的 Azure 多重因素驗證 (MFA)，或使用的 Microsoft 帳戶 (通常@outlook.com),您需要進行永久性中所有的角色。 註冊 MFA 啟動期間，系統會要求符合資格的管理員。

現在，您有合格角色的使用者，讓他們知道他們可以根據[如何啟用或停用角色](active-directory-privileged-identity-management-how-to-activate-role.md)中的指示來啟動它。

## <a name="remove-a-user-from-a-role"></a>移除使用者的角色

您可以從符合資格的角色指派]，移除使用者，但請務必永遠是至少有一個永久性的全域管理員的使用者。

請遵循這些步驟來移除特定使用者的角色︰

1. 選取 Azure AD PIM 儀表板中的角色，或按一下 [**管理員角色中的使用者**] 按鈕，瀏覽至 [角色] 清單中的角色。
2. 按一下 [使用者] 清單中的使用者。
3. 按一下 [**移除**]。 郵件會要求您確認。
4. 按一下 [ **]**若要移除使用者的角色。

如果您不確定使用者仍需要其角色指派，您可以[開始角色的存取檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
