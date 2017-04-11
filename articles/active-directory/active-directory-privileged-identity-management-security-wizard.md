<properties
   pageTitle="Azure AD 有權限的身分識別管理安全性精靈"
   description="第一次您使用的 Azure Active Directory 獲授限身分識別管理副檔名，您就會顯示安全性精靈。 本文將說明使用精靈的步驟。"
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
   ms.date="07/01/2016"
   ms.author="kgremban"/>

# <a name="the-azure-ad-privileged-identity-management-security-wizard"></a>Azure AD 有權限的身分識別管理安全性精靈

如果您是為您的組織執行 Azure 權限的身分識別管理 (PIM) 的第一個人員，您就會顯示在使用精靈。 精靈會協助您瞭解安全性風險的權限的身分識別，以及如何使用 PIM 降低這些風險。 您不需要變更現有的角色指派在精靈中，如果您想要稍後再執行。

## <a name="what-to-expect"></a>預期狀況

您的組織開始使用 PIM 前，所有的角色指派是永久︰ 使用者一律是擔任以下角色即使他們不目前需要他們的權限。  精靈的第一個步驟會顯示您高權角色的清單，且這些角色目前位於多少使用者。 您可以深入瞭解特定角色若要進一步瞭解使用者如果一個或多個不甚。

精靈的第二個步驟可讓您得以變更系統管理員的角色指派。  

> [AZURE.WARNING]請務必您必須至少有一個的全域管理員，而且一個以上的權限的角色系統管理員，使用組織帳戶 （不以 Microsoft 帳戶）。 如果有一個權限的角色系統管理員，組織會無法管理 PIM 如果刪除該帳戶。
> 此外，保留角色指派永久如果使用者擁有 Microsoft 帳戶 （，用來登入 Microsoft 服務，例如 Skype 與 Outlook.com 帳戶）。 如果您打算具備該角色的啟動 MFA，該使用者會被鎖定。


您所做的變更之後，會不會再顯示精靈。 下次當您或其他有權限的角色管理員使用 PIM，您會看到 [PIM 儀表板。  

- 如果您想要新增或移除角色的使用者或變更工作分派從永久以符合資格，閱讀更多如何[新增或移除使用者的角色](active-directory-privileged-identity-management-how-to-add-role-to-user.md)。
- 如果您想要授與其他使用者存取權管理 PIM，閱讀更多如何[授與存取權管理 PIM 中](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。



## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
