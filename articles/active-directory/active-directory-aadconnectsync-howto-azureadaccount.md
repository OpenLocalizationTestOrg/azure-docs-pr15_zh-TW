<properties
    pageTitle="Azure AD Connect 同步處理︰ 如何管理 Azure AD 服務帳戶 |Microsoft Azure"
    description="本主題的文件還原 Azure AD 服務帳戶。"
    services="active-directory"
    keywords="AADSTS70002、 AADSTS50054，如何重設 Azure AD Connect 同步處理連接器服務帳戶的密碼"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Azure AD Connect 同步處理︰ 如何管理 Azure AD 服務帳戶
Azure AD 連接器使用的服務帳戶應該是免費的服務。 如果您需要重設其認證，然後本主題適用於您。 例如，如果全域管理員具有錯誤重設使用 PowerShell 服務帳戶的密碼。

## <a name="reset-the-credentials"></a>重設認證
如果定義 Azure AD 連接器上的服務帳戶無法連絡 Azure AD 驗證問題，就可以重設密碼。

1. Azure AD Connect 同步處理伺服器登入並開始 PowerShell。
2. 執行`Add-ADSyncAADServiceAccount`。  
![PowerShell cmdlet addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. 提供 Azure AD 全域管理員認證。

這個指令程式會重設服務帳戶的密碼，並在 Azure AD 和中同步處理引擎更新。

## <a name="known-issues-these-steps-can-solve"></a>這些步驟可以解決的已知的問題
本節是錯誤報告所修正 Azure AD 服務帳戶重設認證的客戶的清單。

-----------
事件 6900  
伺服器處理密碼變更通知時發生意外的錯誤︰  
AADSTS70002︰ 錯誤驗證認證。 AADSTS50054︰ 使用舊密碼進行驗證。

----------
事件 659  
擷取密碼原則同步處理設定時發生錯誤。 Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002︰ 錯誤驗證認證。 AADSTS50054︰ 使用舊密碼進行驗證。

## <a name="next-steps"></a>後續步驟

**概觀主題**

- [Azure AD Connect 同步處理︰ 了解並自訂同步處理](active-directory-aadconnectsync-whatis.md)
- [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
