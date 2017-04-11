<properties
   pageTitle="如何授與存取權 PIM |Microsoft Azure"
   description="瞭解如何新增副檔名為 Azure Active Directory 獲授限身分識別管理使用者的角色，讓他們可以管理 PIM。"
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
   ms.date="09/22/2016"
   ms.author="kgremban"/>

# <a name="how-to-give-access-to-manage-azure-ad-privileged-identity-management"></a>若要授與存取權管理 Azure AD 有權限的身分識別管理方式

自動可讓組織 Azure AD 有權限的身分識別管理 (PIM) 的全域管理員取得角色指派及 PIM 存取。 沒有任何人取得寫入存取權根據預設，不過，包括其他全域系統管理員。 其他全域管理員、 安全性系統管理員及安全性讀者有 Azure AD PIM 唯讀存取。 若要讓 PIM 有權存取，第一位使用者可以指定其他人**有權限的角色管理員**角色。 這項作業 PIM 本身，必須透過，且無法變更透過 PowerShell 或其他入口網站。

> [AZURE.NOTE] 管理 Azure AD PIM 需要 Azure MFA。 由於 Microsoft 帳戶無法註冊 Azure MFA，使用 Microsoft 帳戶登入的使用者無法存取 Azure AD PIM。

請確定有兩個以上的使用者權限的角色管理員角色，以避免一位使用者已鎖定，或刪除帳戶。

## <a name="give-another-user-access-to-manage-pim"></a>授與管理 PIM 的另一個使用者存取權

1. 登入[Azure 入口網站](https://portal.azure.com/)，然後選取儀表板上的**Azure AD 有權限的身分識別管理**應用程式。
2. 選取 [**管理有權限的角色** > **有權限的角色管理員** > **新增**。

    ![新增權限的角色管理員的螢幕擷取畫面][1]

4. 在新增受管理的使用者刀中，已完成步驟 1。 選取 [步驟 2 的使用者，然後**選取**搜尋您想要新增的使用者]。

    ![選取使用者的螢幕擷取畫面][2]

6. 從搜尋結果中，選取使用者，然後按一下 [**完成**]。
7. 按一下**[確定**] 以儲存您的選取範圍。 您所選取的使用者會出現在清單中的權限的角色系統管理員。

    - 每當您新的角色指派給其他人時，他們會自動設定為合格啟動角色。 如果您想要進行永久性角色中，按一下 [在清單中的使用者]。 在 [使用者資訊] 功能表中，選取 [**讓權限**]。

8. 若要[快速入門 Azure AD 有權限的身分識別管理](active-directory-privileged-identity-management-getting-started.md)傳送使用者的連結。


## <a name="remove-another-users-access-rights-for-managing-pim"></a>移除其他使用者的存取權限管理 PIM

您有權限的角色管理員角色中移除人員之前，務必仍會有兩個使用者指派給它。

1. 在 PIM 儀表板中按一下該角色的**權限的角色系統管理員**。  會顯示目前在該角色的使用者清單。
2. 按一下 [使用者] 清單中的使用者。
3. 按一下 [**移除**]。  您會看到一則確認訊息。
4. 按一下 [ **]**若要移除該角色的使用者。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_add_PRA.png
[2]: ./media/active-directory-privileged-identity-management-how-to-give-access-to-pim/PIM_select_users.png
