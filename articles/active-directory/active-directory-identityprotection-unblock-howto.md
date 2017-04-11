<properties
    pageTitle="Azure Active Directory 身分識別保護-如何解除封鎖使用者 |Microsoft Azure"
    description="瞭解如何解除封鎖使用者已封鎖的 Azure Active Directory 身分識別保護原則。"
    services="active-directory"
    keywords="azure active directory 身分識別保護，解除封鎖使用者"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection---how-to-unblock-users"></a>Azure Active Directory 身分識別保護-如何解除封鎖使用者

Azure Active Directory 身分識別保護，您可以設定封鎖使用者，如果已設定的條件滿足原則。 一般而言，封鎖的使用者連絡人服務台成為解除封鎖。 本主題說明若要解除封鎖的使用者，您可以執行的步驟。


## <a name="determine-the-reason-for-blocking"></a>決定封鎖的原因

若要解除封鎖使用者第一個步驟是，您需要判斷已封鎖使用者，因為接下來的步驟會根據的原則的類型。 Azure Active Directory 身分識別保護，使用者可以 [封鎖的登入風險原則或使用者風險原則。 

您可以取得已封鎖使用者使呈現給使用者在嘗試登入] 對話方塊中的 [標題的原則的類型︰

|原則 | 使用者] 對話方塊|
|--- | --- |
|登入風險 | ![封鎖的登入](./media/active-directory-identityprotection-unblock-howto/02.png) |
|使用者風險 | ![封鎖的帳戶](./media/active-directory-identityprotection-unblock-howto/104.png) |


封鎖的使用者︰

- 登入風險原則亦稱為可疑的登入
- 使用者風險原則亦稱為風險的帳戶

 
## <a name="unblocking-suspicious-sign-ins"></a>解除封鎖可疑登增益集

若要解除可疑的登入，您有下列選項︰

1. **登入從熟悉的位置或裝置**-封鎖可疑登增益集的常見原因是登入嘗試從熟悉的位置或裝置。 您的使用者可以快速判斷嘗試登入從熟悉的位置或裝置是否為封鎖的原因。


3. **排除原則**-如果您認為您的登入原則的目前設定導致特定使用者的問題，您可以從其排除使用者。 請參閱[登入風險原則](active-directory-identityprotection.md#sign-in-risk-policy)，如需詳細資訊。
 
4. **停用原則**-如果您認為您的原則設定導致所有使用者的問題，您可以停用原則。 請參閱[登入風險原則](active-directory-identityprotection.md#sign-in-risk-policy)，如需詳細資訊。


## <a name="unblocking-accounts-at-risk"></a>解除封鎖的帳戶風險

若要解除風險的帳戶，您有下列選項︰

1. **重設密碼**-您可以重設使用者的密碼。 請參閱[手動安全密碼重設](active-directory-identityprotection.md#manual-secure-password-reset)如需詳細資訊。

2. **關閉所有的風險事件**-使用者風險原則會封鎖使用者，如果已封鎖存取的設定的使用者風險層級。 您也可以縮減使用者手動關閉風險層級的報告風險事件。 如需詳細資訊，請參閱[手動關閉風險事件](active-directory-identityprotection.md#closing-risk-events-manually)。

3. **排除原則**-如果您認為您的登入原則的目前設定導致特定使用者的問題，您可以從其排除使用者。 請參閱[使用者風險原則](active-directory-identityprotection.md#user-risk-policy)，如需詳細資訊。
 
4. **停用原則**-如果您認為您的原則設定導致所有使用者的問題，您可以停用原則。 請參閱[使用者風險原則](active-directory-identityprotection.md#user-risk-policy)，如需詳細資訊。




## <a name="next-steps"></a>後續步驟

 您想要進一步瞭解 Azure AD 身分識別保護功能？ 請參閱[Azure Active Directory 身分識別保護](active-directory-identityprotection.md)。
 

