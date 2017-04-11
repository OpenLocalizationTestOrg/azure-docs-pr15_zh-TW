<properties
    pageTitle="未獲授權的使用情況報告 |Microsoft Azure"
    description="未獲授權的使用狀況報表有助於識別未授權的使用者使用的支付 Azure AD 功能。"
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="unlicensed-usage-report"></a>未獲授權的使用情況報告

未獲授權的使用狀況報表有助於識別未授權的使用者使用的支付 Azure AD 功能。 這個選項可讓您改善使用的您已購買的授權和識別您知道您可能需要額外的授權。 

報表 30 天內顯示付費的各項功能的作用中的使用方式。 

## <a name="report-structure"></a>報告結構
 
| 資料行名稱          |    描述 |
| :--                  | :--         |
| 未獲授權的使用者      |    使用者的名稱 |
| 功能              | 功能名稱。 例如︰ 條件的存取權 |
| 存取應用程式 | 正在使用功能存取應用程式的名稱。 例如︰ Office 365 SharePoint Online |

 
> [AZURE.NOTE] 如果已刪除的使用者帳戶 「 未授權的使用者] 欄會填入的識別碼，例如 1003000090D8B285


## <a name="conditional-access-feature"></a>條件的存取功能

存取條件存取原則套用如果沒有 Azure AD 進階版授權的服務時，會標示未授權的使用者。 

這適用於 MFA / 位置原則，以及裝置原則使用 Intune。
 

## <a name="see-also"></a>另請參閱

- [使用條件存取 Office 365 與其他的 Azure Active Directory 連線應用程式](active-directory-conditional-access.md)
- [Azure ad 條件 access 快速入門](active-directory-conditional-access-azuread-connected-apps.md) 


