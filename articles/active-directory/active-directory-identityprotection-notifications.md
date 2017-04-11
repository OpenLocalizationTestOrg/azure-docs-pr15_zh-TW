<properties
    pageTitle="Azure Active Directory 身分識別保護通知 |Microsoft Azure"
    description="瞭解如何通知支援您調查的活動。"
    services="active-directory"
    keywords="azure active directory 身分識別保護]，[管理應用程式、 安全性、 風險、 風險層級、 弱點、 安全性原則的雲端應用程式探索"
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

#<a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory 身分識別保護通知 


Azure AD 身分識別保護傳送，協助您管理使用者風險及風險事件的自動化的通知電子郵件的兩種︰

- 使用者遭到盜用提醒的電子郵件

- 每週摘要電子郵件

## <a name="user-compromised-alert-email"></a>使用者遭到盜用提醒的電子郵件

Azure AD 身分識別保護識別遭到盜用帳戶時，會產生使用者洩漏的電子郵件提醒。 電子郵件包含使用者的身分識別保護儀表板中的風險報表加上標幟的連結。 我們建議您立即調查的通知遭到盜用。


## <a name="weekly-digest-email"></a>每週摘要電子郵件

每週的摘要電子郵件中包含新的風險事件的摘要。<br>
包括︰

- 使用者風險
- 可疑的活動
- 偵測到的弱點
- 相關的報表，身分識別保護中的連結


<br>
![修復](./media/active-directory-identityprotection-notifications/400.png "Remediation")
<br> 

您可以切換傳送的每週的摘要電子郵件。
<br><br>
![使用者風險](./media/active-directory-identityprotection-notifications/62.png "User risks")
<br>
 

**若要開啟 [相關的設定] 對話方塊**︰

1. **Azure AD 身分識別保護**刀中，按一下 [**設定**]。
<br><br>
![使用者風險原則](./media/active-directory-identityprotection-notifications/401.png "User risk policy")
<br>

2. 在 [**一般**] 區段中，按一下 [**通知**]。
<br><br>
![使用者風險原則](./media/active-directory-identityprotection-notifications/405.png "User risk policy")
<br>




## <a name="see-also"></a>另請參閱

- [Azure Active Directory 身分識別保護](active-directory-identityprotection.md) 

