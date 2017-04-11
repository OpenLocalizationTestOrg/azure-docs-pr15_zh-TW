<properties
   pageTitle="角色 PIM |Microsoft Azure"
   description="瞭解使用何種角色的權限的身分識別與 Azure 權限的身分識別管理副檔名。"
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

# <a name="roles-in-azure-ad-privileged-identity-management"></a>Azure AD 角色的權限的身分識別管理

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

您可以將使用者指派不同的系統管理角色中 Azure AD 您組織中。 這些角色指派控制工作，例如新增或移除使用者或變更服務設定]，使用者就可以在 Office 365 其他 Microsoft Online Services 和連接的應用程式的 Azure AD 上執行。  

全域管理員可以更新的使用者會**永久**指派給角色 Azure AD，例如使用 PowerShell cmdlet`Add-MsolRoleMember`和`Remove-MsolRoleMember`，或透過傳統入口網站[指派管理員角色 Azure Active Directory 中的](active-directory-assign-admin-roles.md)所述。

Azure AD 權限的身分識別管理 (PIM) Azure AD 中管理使用者的權限存取的原則。 PIM 中 Azure AD，將使用者指派給一或多個角色，您可以分派其他人會永久角色，或符合資格的角色。 當使用者將會永久指派給角色，或啟動合格的角色工作分派]，然後他們可以指派給角色的權限與管理 Azure Active Directory、 Office 365 和其他應用程式。

沒有差別提供給符合資格的角色指派與永久性與其他人的存取權。 唯一的差別有些人隨時都不需要存取權。 他們所做有資格申請角色時，可以將其開啟，並關閉時需要。

## <a name="roles-managed-in-pim"></a>在 PIM 管理角色

有權限的身分識別管理可讓您將使用者指派至常見的系統管理員角色，包括︰


- **全域管理員**（也稱為公司系統管理員） 有權存取所有系統管理功能。 您可以在組織中有一個以上的全域管理員。 若要自動購買 Office 365 註冊的人員會變成全域管理員。
- **有權限的角色管理員**管理 Azure AD PIM，並更新為其他使用者的角色指派。  
- **計費管理員**採購、 管理訂閱、 管理支援票證及監視服務健康狀況。
- **密碼管理員**重設密碼、 管理服務要求和監視服務健康狀況。 密碼管理員限於重設使用者的密碼。
- **服務管理員**管理服務要求和監視服務健康狀況。

  > [AZURE.NOTE] 如果您使用的 Office 365，然後之前的服務管理員角色指派給使用者，第一次為使用者指派系統管理權限至服務，例如 Exchange Online。

- **使用者管理管理員**重設密碼、 監視服務健康狀況，及管理使用者帳戶、 使用者群組和服務要求。 使用者管理管理員無法刪除的全域管理員、 建立其他的管理員角色，或是為全域、 計費和服務管理員重設密碼。
- **Exchange 系統管理員**具有 Exchange Online 的管理權限透過 Exchange 系統管理中心 (EAC)，並可執行幾乎所有工作在 Exchange Online。
- **SharePoint 系統管理員**透過 SharePoint Online 系統管理中心行使 SharePoint online 的管理權限，並可在 SharePoint Online 中執行幾乎所有工作。
- **商務用 Skype 管理員**行使商務用 Skype 系統管理中心，透過商務用 skype 的管理權限，並可在 Skype 中執行幾乎所有工作的商務用 Online。

閱讀下列文章，如需詳細資訊[Azure AD 中的指派管理員角色](active-directory-assign-admin-roles.md)和[Office 365 中指派管理員角色](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504)。

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


從 PIM，您可以[指派這些角色的使用者](active-directory-privileged-identity-management-how-to-add-role-to-user.md)，讓使用者可以[啟動時所需的角色](active-directory-privileged-identity-management-how-to-activate-role.md)。

如果您要授與管理 PIM 本身中的另一個使用者存取權，會 PIM 要求使用者擁有的角色是進一步說明如何[授與存取權 PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)。


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>不在 PIM 管理角色

除了上面所提 Exchange Online 或 SharePoint Online 中的角色 Azure AD 不會顯示，然後，因此不會顯示在 PIM。 如需有關如何變更精密的角色指派這些 Office 365 服務的詳細資訊，請參閱[在 Office 365 中的權限](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d)。

Azure 訂閱及資源群組也不會顯示 Azure AD。 若要管理 Azure 訂閱，請參閱[如何新增或變更 Azure 管理員角色](../billing-add-change-azure-subscription-administrator.md)及 Azure RBAC 的詳細資訊請參閱[Azure Role-Based Access 控制項](role-based-access-control-configure.md)。

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>使用者角色及登入
某些 Microsoft 服務和應用程式，指派給角色的使用者可能不能是系統管理員的使用者啟用。

Azure 傳統入口網站的存取要求使用者是服務管理員或共同管理員 Azure 的訂閱，即使使用者不需要管理 Azure 訂閱。  例如，Azure AD 傳統入口網站中的管理設定的設定，使用者必須同時 Azure AD 中的全域管理員和 Azure 訂閱的訂閱共同管理員。  若要瞭解如何將使用者新增至 Azure 訂閱，請參閱[如何新增或變更 Azure 管理員角色](../billing-add-change-azure-subscription-administrator.md)。

存取 Microsoft 線上服務可能會要求使用者指派授權之前他們可以開啟該服務的入口網站，或執行系統管理工作。

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Azure AD 中指派給使用者的授權

1. 登入 [Azure 傳統入口網站] (http://manage.windowsazure.com) 全域管理員帳戶的共同管理員帳戶。
2. 從主功能表中選取**所有項目**。
3. 選取您想要使用的目錄，且具有與其相關聯的授權。
4. 選取 [**授權**]。 會顯示可用的授權的清單。
5. 選取含有您想要散發之授權的授權方案。
6. 選取 [**指派給使用者**。
7. 選取您想要指派授權的使用者。
8. 按一下 [**指派**] 按鈕。  使用者可以登入 Azure。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
