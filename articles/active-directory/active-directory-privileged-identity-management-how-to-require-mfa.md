<properties
   pageTitle="如何將需要多重因素驗證 |Microsoft Azure"
   description="瞭解如何需要多重因素驗證 (MFA) 的權限的身分識別與 Azure Active Directory 有權限身分識別管理副檔名。"
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

# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>如何將需要 MFA Azure AD 有權限的身分識別管理

我們建議您在針對您的系統管理員的所有需要多重因素驗證 (MFA)。 這會減少攻擊，因為洩漏密碼的風險。

您可以要求使用者完成 MFA 挑戰，當使用者登入。 部落格文章[for Office 365 和 Azure 的 MFA MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/)比較中包含哪些 Office 和 Azure 訂閱]，在 Microsoft Azure 多重因素驗證服務中包含的功能。

您也可以要求使用者在他們啟動 Azure AD PIM 的角色時，會完成 MFA 挑戰。 如此一來，如果使用者未完成 MFA 挑戰，他們登入，他們會會提示您這麼做，PIM。

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>需要 MFA Azure AD 權限的身分識別管理

當您管理 PIM 有權限的角色管理員的身分識別時，您可能會看到有權限的帳戶建議 MFA 的通知。 按一下 [PIM 儀表板中的安全性提醒中，新的刀隨即會開啟應該需要 MFA 的系統管理員帳戶的清單。  您可以要求 MFA 選取多個角色，然後按一下 [**修正**] 按鈕，或您可以按一下個別角色] 旁的省略符號，然後按一下 [**修正**] 按鈕。

> [AZURE.IMPORTANT] 現在，Azure MFA，才能使用透過公司或學校帳戶，沒有 Microsoft 帳戶 （通常個人帳戶，可用來登入 Microsoft 服務，例如 Skype、 Xbox、 Outlook.com 等。），以滑鼠右鍵。 因此，使用 Microsoft 帳戶的任何人都不能符合資格的管理員，因為他們無法使用 MFA 啟動其角色。 如果這些使用者需要繼續管理負載使用 Microsoft 帳戶，提高權限進行永久性管理員現在。

此外，您可以變更特定角色的 MFA 需求 PIM 儀表板的 [角色] 區段中按一下。 然後按一下 [**設定**中的角色刀，然後選取 [多重因素驗證下的 [**啟用**。

## <a name="how-azure-ad-pim-validates-mfa"></a>Azure AD PIM 驗證 MFA 的方式

有兩個驗證 MFA 使用者啟動的角色時的選項。

要啟動的權限的角色的使用者依賴 Azure MFA 時，最簡單的選項。 若要這麼做，請先核取這些使用者的授權，如有必要，，而且註冊 Azure MFA。 [快速入門 Azure 多重因素驗證，在雲端](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)位於如何執行此動作的詳細資訊。 建議您，但不是需要，您設定 Azure AD 強制 MFA 執行這些使用者，當使用者登入。 這是因為 MFA 檢查項目會由 Azure AD PIM 本身。

或者，如果使用者進行驗證內部部署，您可以讓您負責 MFA 的身分識別提供者。 例如，如果您已設定 AD 要求存取 Azure AD，[保護雲端資源 Azure 多重因素驗證與 AD FS](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md)之前的智慧卡型驗證的 Federation Services 包含指示設定 AD FS 傳送至 Azure AD 宣告。 當使用者嘗試啟動角色時，Azure AD PIM 就會接受的 MFA 已驗證的使用者後，會收到適當的宣告。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]
