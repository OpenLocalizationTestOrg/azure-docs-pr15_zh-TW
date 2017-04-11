<properties
    pageTitle="Azure AD Connect 同步處理︰ 屬性同步處理到 Azure Active Directory |Microsoft Azure"
    description="列出到 Azure Active Directory 同步處理的屬性。"
    services="active-directory"
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
    ms.date="09/13/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect 同步處理︰ 到 Azure Active Directory 同步處理的屬性
本主題列出 Azure AD Connect 同步處理，同步處理的屬性。  
屬性分組相關 Azure AD 應用程式。

## <a name="attributes-to-synchronize"></a>若要同步處理的屬性
常見的問題是*什麼是同步處理的最小值屬性的清單*。 預設與建議的方法是要保留的預設屬性，以便完整的 GAL （全域通訊清單） 可以用來建構在雲端，並取得在 Office 365 工作負載的所有功能。 在某些情況下，您的組織不想同步處理到雲端之後包含這些屬性機密一些屬性或 PII （個人識別資訊） 資料，例如在此範例中︰  
![不正確的屬性](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

在此情況下，開始本主題中的屬性的清單，找出想要包含敏感或 PII 資料，而且無法同步處理這些屬性。 使用[Azure AD 應用程式和屬性篩選](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)在安裝期間，然後取消選取這些屬性。

>[AZURE.WARNING] 當取消選取屬性，您應該請務必謹慎，並只取消選取絕對不能同步處理這些屬性。 取消選取其他屬性可能會有負面影響的功能。

## <a name="office-365-proplus"></a>Office 365 專業增強版

| 屬性名稱| 使用者| 註解 |
| --- | :-: | --- |
| accountEnabled| X| 定義如果啟用帳戶。|
| cn| X|  |
| 顯示名稱| X|  |
| objectSID| X| 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| pwdLastSet| X| 機械屬性。 用來知道失效已經發行的權杖。 使用密碼同步處理] 與 [同盟。|
| sourceAnchor| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| usageLocation| X| 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userPrincipalName| X| UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|

## <a name="exchange-online"></a>Exchange Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | 定義如果啟用帳戶。|
| 小幫手| X| X|  |  |
| authOrig| X| X| X|  |
| c| X| X|  |  |
| cn| X|  | X|  |
| 共同| X| X|  |  |
| 公司| X| X|  |  |
| 國碼| X| X|  |  |
| 部門| X| X|  |  |
| 描述| X| X| X|  |
| 顯示名稱| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| 住家電話| X| X|  |  |
| 資訊| X| X| X| 這個屬性目前未使用的群組。|
| 縮寫| X| X|  |  |
| l| X| X|  |  |
| legacyExchangeDN| X| X| X|  |
| mailNickname| X| X| X|  |
| mangedBy|  |  | X|  |
| 管理員| X| X|  |  |
| 成員|  |  | X|  |
| 行動裝置| X| X|  |  |
| msDS HABSeniorityIndex| X| X| X|  |
| msDS PhoneticDisplayName| X| X| X|  |
| msExchArchiveGUID| X|  |  |  |
| msExchArchiveName| X|  |  |  |
| msExchAssistantName| X| X|  |  |
| msExchAuditAdmin| X|  |  |  |
| msExchAuditDelegate| X|  |  |  |
| msExchAuditDelegateAdmin| X|  |  |  |
| msExchAuditOwner| X|  |  |  |
| msExchBlockedSendersHash| X| X|  |  |
| msExchBypassAudit| X|  |  |  |
| msExchCoManagedByLink|  |  | X|  |
| msExchDelegateListLink| X|  |  |  |
| msExchELCExpirySuspensionEnd| X|  |  |  |
| msExchELCExpirySuspensionStart| X|  |  |  |
| msExchELCMailboxFlags| X|  |  |  |
| msExchEnableModeration| X|  | X|  |
| msExchExtensionCustomAttribute1| X| X| X| Exchange online 目前無法使用這個屬性。 |
| msExchExtensionCustomAttribute2| X| X| X| Exchange online 目前無法使用這個屬性。 |
| msExchExtensionCustomAttribute3| X| X| X| Exchange online 目前無法使用這個屬性。 |
| msExchExtensionCustomAttribute4| X| X| X| Exchange online 目前無法使用這個屬性。 |
| msExchExtensionCustomAttribute5| X| X| X| Exchange online 目前無法使用這個屬性。 |
| msExchHideFromAddressLists| X| X| X|  |
| msExchImmutableID| X|  |  |  |
| msExchLitigationHoldDate| X| X| X|  |
| msExchLitigationHoldOwner| X| X| X|  |
| msExchMailboxAuditEnable| X|  |  |  |
| msExchMailboxAuditLogAgeLimit| X|  |  |  |
| msExchMailboxGuid| X|  |  |  |
| msExchModeratedByLink| X| X| X|  |
| msExchModerationFlags| X| X| X|  |
| msExchRecipientDisplayType| X| X| X|  |
| msExchRecipientTypeDetails| X| X| X|  |
| msExchRemoteRecipientType| X|  |  |  |
| msExchRequireAuthToSendTo| X| X| X|  |
| msExchResourceCapacity| X|  |  |  |
| msExchResourceDisplay| X|  |  |  |
| msExchResourceMetaData| X|  |  |  |
| msExchResourceSearchProperties| X|  |  |  |
| msExchRetentionComment| X| X| X|  |
| msExchRetentionURL| X| X| X|  |
| msExchSafeRecipientsHash| X| X|  |  |
| msExchSafeSendersHash| X| X|  |  |
| msExchSenderHintTranslations| X| X| X|  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| msExchUserHoldPolicies| X|  |  |  |
| msOrg IsOrganizational|  |  | X|  |
| objectSID| X|  | X| 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherTelephone| X| X|  |  |
| 呼叫器| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| 郵遞區號| X| X|  |  |
| proxyAddresses| X| X| X|  |
| publicDelegates| X| X| X|  |
| pwdLastSet| X|  |  | 機械屬性。 用來知道失效已經發行的權杖。 使用密碼同步處理] 與 [同盟。|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| 衍生自 groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| st| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| 標題| X| X|  |  |
| unauthOrig| X| X| X|  |
| usageLocation| X|  |  | 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userCertificate| X| X|  |  |
| userPrincipalName| X|  |  | UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|
| userSMIMECertificates| X| X|  |  |
| wWWHomePage| X| X|  |  |

## <a name="sharepoint-online"></a>SharePoint Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | 定義如果啟用帳戶。|
| authOrig| X| X| X|  |
| c| X| X|  |  |
| cn| X|  | X|  |
| 共同| X| X|  |  |
| 公司| X| X|  |  |
| 國碼| X| X|  |  |
| 部門| X| X|  |  |
| 描述| X| X| X|  |
| 顯示名稱| X| X| X|  |
| dLMemRejectPerms| X| X| X|  |
| dLMemSubmitPerms| X| X| X|  |
| extensionAttribute1| X| X| X|  |
| extensionAttribute10| X| X| X|  |
| extensionAttribute11| X| X| X|  |
| extensionAttribute12| X| X| X|  |
| extensionAttribute13| X| X| X|  |
| extensionAttribute14| X| X| X|  |
| extensionAttribute15| X| X| X|  |
| extensionAttribute2| X| X| X|  |
| extensionAttribute3| X| X| X|  |
| extensionAttribute4| X| X| X|  |
| extensionAttribute5| X| X| X|  |
| extensionAttribute6| X| X| X|  |
| extensionAttribute7| X| X| X|  |
| extensionAttribute8| X| X| X|  |
| extensionAttribute9| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| hideDLMembership|  |  | X|  |
| 住家電話| X| X|  |  |
| 資訊| X| X| X|  |
| 縮寫| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| 郵件| X| X| X|  |
| mailnickname| X| X| X|  |
| managedBy|  |  | X|  |
| 管理員| X| X|  |  |
| 成員|  |  | X|  |
| 單獨| X| X|  |  |
| 行動裝置| X| X|  |  |
| msExchTeamMailboxExpiration| X|  |  |  |
| msExchTeamMailboxOwners| X|  |  |  |
| msExchTeamMailboxSharePointLinkedBy| X|  |  |  |
| msExchTeamMailboxSharePointUrl| X|  |  |  |
| objectSID| X|  | X| 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| oOFReplyToOriginator|  |  | X|  |
| otherFacsimileTelephone| X| X|  |  |
| otherHomePhone| X| X|  |  |
| otherIpPhone| X| X|  |  |
| otherMobile| X| X|  |  |
| otherPager| X| X|  |  |
| otherTelephone| X| X|  |  |
| 呼叫器| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| 郵遞區號| X| X|  |  |
| postOfficeBox| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | 機械屬性。 用來知道失效已經發行的權杖。 使用密碼同步處理] 與 [同盟。|
| reportToOriginator|  |  | X|  |
| reportToOwner|  |  | X|  |
| securityEnabled|  |  | X| 衍生自 groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| st| X| X|  |  |
| streetAddress| X| X|  |  |
| targetAddress| X| X|  |  |
| telephoneAssistant| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| 標題| X| X|  |  |
| unauthOrig| X| X| X|  |
| url| X| X|  |  |
| usageLocation| X|  |  | 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userPrincipalName| X|  |  | UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|
| wWWHomePage| X| X|  |  |

## <a name="lync-online"></a>Lync Online

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | 定義如果啟用帳戶。|
| c| X| X|  |  |
| cn| X|  | X|  |
| 共同| X| X|  |  |
| 公司| X| X|  |  |
| 部門| X| X|  |  |
| 描述| X| X| X|  |
| 顯示名稱| X| X| X|  |
| facsimiletelephonenumber| X| X| X|  |
| givenName| X| X|  |  |
| 住家電話| X| X|  |  |
| ipPhone| X| X|  |  |
| l| X| X|  |  |
| 郵件| X| X| X|  |
| mailNickname| X| X| X|  |
| managedBy|  |  | X|  |
| 管理員| X| X|  |  |
| 成員|  |  | X|  |
| 行動裝置| X| X|  |  |
| msExchHideFromAddressLists| X| X| X|  |
| 受影響 ApplicationOptions| X|  |  |  |
| 受影響 DeploymentLocator| X| X|  |  |
| 受影響線條| X| X|  |  |
| 受影響 OptionFlags| X| X|  |  |
| 受影響 OwnerUrn| X|  |  |  |
| 受影響 PrimaryUserAddress| X| X|  |  |
| 受影響 Msrtcsip-userenabled| X| X|  |  |
| objectSID| X|  | X| 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| otherTelephone| X| X|  |  |
| physicalDeliveryOfficeName| X| X|  |  |
| 郵遞區號| X| X|  |  |
| preferredLanguage| X|  |  |  |
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | 機械屬性。 用來知道失效已經發行的權杖。 使用密碼同步處理] 與 [同盟。|
| securityEnabled|  |  | X| 衍生自 groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| st| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| thumbnailphoto| X| X|  |  |
| 標題| X| X|  |  |
| usageLocation| X|  |  | 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userPrincipalName| X|  |  | UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|
| wWWHomePage| X| X|  |  |

## <a name="azure-rms"></a>Azure RMS

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | 定義如果啟用帳戶。|
| cn| X|  | X| 常見的名稱或別名。 最常用的前置詞 [郵件] 的值。|
| 顯示名稱| X| X| X| 表示通常做為好記的名稱 （名字姓氏） 中所顯示的名稱的字串。|
| 郵件| X| X| X| 完整的電子郵件地址。|
| 成員|  |  | X|  |
| objectSID| X|  | X| 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| proxyAddresses| X| X| X| 機械屬性。 使用 Azure AD。 包含所有的次要電子郵件地址的使用者。|
| pwdLastSet| X|  |  | 機械屬性。 用來知道失效已經發行的權杖。|
| securityEnabled|  |  | X| 從 groupType。|
| sourceAnchor| X| X| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| usageLocation| X|  |  | 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userPrincipalName| X|  |  | 這個 UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|

## <a name="intune"></a>Intune

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | 定義如果啟用帳戶。|
| c| X| X|  |  |
| cn| X|  | X|  |
| 描述| X| X| X|  |
| 顯示名稱| X| X| X|  |
| 郵件| X| X| X|  |
| mailnickname| X| X| X|  |
| 成員|  |  | X|  |
| objectSID| X|  | X| 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | 機械屬性。 用來知道失效已經發行的權杖。 使用密碼同步處理] 與 [同盟。|
| securityEnabled|  |  | X| 衍生自 groupType|
| sourceAnchor| X| X| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| usageLocation| X|  |  | 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userPrincipalName| X|  |  | UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|

## <a name="dynamics-crm"></a>Dynamics CRM

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | 定義如果啟用帳戶。|
| c| X| X|  |  |
| cn| X|  | X|  |
| 共同| X| X|  |  |
| 公司| X| X|  |  |
| 國碼| X| X|  |  |
| 描述| X| X| X|  |
| 顯示名稱| X| X| X|  |
| facsimiletelephonenumber| X| X|  |  |
| givenName| X| X|  |  |
| l| X| X|  |  |
| managedBy|  |  | X|  |
| 管理員| X| X|  |  |
| 成員|  |  | X|  |
| 行動裝置| X| X|  |  |
| objectSID| X|  | X| 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| physicalDeliveryOfficeName| X| X|  |  |
| 郵遞區號| X| X|  |  |
| preferredLanguage| X|  |  |  |
| pwdLastSet| X|  |  | 機械屬性。 用來知道失效已經發行的權杖。 使用密碼同步處理] 與 [同盟。|
| securityEnabled|  |  | X| 衍生自 groupType|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| st| X| X|  |  |
| streetAddress| X| X|  |  |
| telephoneNumber| X| X|  |  |
| 標題| X| X|  |  |
| usageLocation| X|  |  | 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userPrincipalName| X|  |  | UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|

## <a name="3rd-party-applications"></a>第 3 廠商應用程式
此群組是一組用來做為所需的一般工作負載或應用程式的最小屬性的屬性。 它可以用於並未列於另一節的工作量或之非 Microsoft 應用程式。 明確使用下列項目︰

- Yammer （僅限的使用者使用）
- [SharePoint 之類的資源所提供的混合式商務商務 B2B 跨組織共同作業案例](http://go.microsoft.com/fwlink/?LinkId=747036)

此群組是一組可供如果 Azure AD 目錄不是使用 Office 365、 Dynamics 或 Intune 支援的屬性。 有一組小型核心屬性。

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X|  |  | 定義如果啟用帳戶。|
| cn| X|  | X|  |
| 顯示名稱| X| X| X|  |
| givenName| X| X|  |  |
| 郵件| X|  | X|  |
| managedBy|  |  | X|  |
| mailNickName| X| X| X|  |
| 成員|  |  | X|  |
| objectSID| X|  |  | 機械屬性。 用來維護同步處理之間 Azure AD 使用者識別碼 AD 和 AD。|
| proxyAddresses| X| X| X|  |
| pwdLastSet| X|  |  | 機械屬性。 用來知道失效已經發行的權杖。 使用密碼同步處理] 與 [同盟。|
| sn| X| X|  |  |
| sourceAnchor| X| X| X| 機械屬性。 若要維持相加和 Azure AD 之間的關聯性不變的識別碼。|
| usageLocation| X|  |  | 機械屬性。 使用者的國家/地區。 用於授權的指派。|
| userPrincipalName| X|  |  | UPN 的使用者登入 ID。 通常為 [郵件] 相同的值。|

## <a name="windows-10"></a>在 Windows 10
在 Windows 10 網域 computer(device) 同步處理某些 Azure AD 屬性。 案例的詳細資訊，請參閱[連線至 Windows 10 版的 Azure AD 網域裝置體驗](active-directory-azureadjoin-devices-group-policy.md)。 這些屬性自動同步處理，並為應用程式，您可以取消選取不會出現在 Windows 10。 在 Windows 10 網域的電腦是由填入屬性 userCertificate 識別。

| 屬性名稱| 裝置| 註解 |
| --- | :-: | --- |
| accountEnabled| X| |
| deviceTrustType| X| 網域的電腦的硬式編碼值。 |
| 顯示名稱 | X| |
| ms-DS-CreatorSID | X| 也稱為 registeredOwnerReference。|
| objectguid 資訊 | X| 也稱為 deviceID。|
| objectSID | X| 也稱為 onPremisesSecurityIdentifier。|
| operatingSystem | X| 也稱為 deviceOSType。|
| operatingSystemVersion | X| 也稱為 deviceOSVersion。|
| userCertificate | X| |

除了您所選取的其他應用程式，是這些**使用者**的屬性。  

| 屬性名稱| 使用者| 註解 |
| --- | :-: | --- |
| domainFQDN| X| 也稱為 dnsDomainName。 例如，contoso.com。|
| domainNetBios| X| 也稱為 netBiosName。 例如，CONTOSO。|

## <a name="exchange-hybrid-writeback"></a>Exchange 混合式回寫
當您選取要啟用**Exchange 混合式部署**時，這些屬性從 Azure AD 回會寫入到內部部署的 Active Directory。 根據您的 Exchange 版本，可能會同步處理較少的屬性。

| 屬性名稱| 使用者| 連絡人| 群組| 註解 |
| --- | :-: | :-: | :-: | --- |
| msDS ExternalDirectoryObjectID| X|  |  | 衍生自 cloudAnchor Azure AD 中。 這個屬性會新增在 Exchange 2016。|
| msExchArchiveStatus| X|  |  | 線上封存︰ 可讓客戶封存郵件。|
| msExchBlockedSendersHash| X|  |  | 篩選︰ 撰寫回內部部署篩選和線上安全及封鎖寄件者資料從用戶端。|
| msExchSafeRecipientsHash| X|  |  | 篩選︰ 撰寫回內部部署篩選和線上安全及封鎖寄件者資料從用戶端。|
| msExchSafeSendersHash| X|  |  | 篩選︰ 撰寫回內部部署篩選和線上安全及封鎖寄件者資料從用戶端。|
| msExchUCVoiceMailSettings| X|  |  | 啟用整合通訊 (UM)-Online 語音信箱︰ 使用 Microsoft Lync Server，表示至 Lync Server 整合內部部署使用者在線上服務擁有語音信箱。|
| msExchUserHoldPolicies| X|  |  | 訴訟保留︰ 可讓雲端服務，來決定哪些使用者正在訴訟保留。|
| proxyAddresses| X| X| X| 僅限 Exchange Online 中的地址會插入 x500。|

## <a name="device-writeback"></a>裝置回寫
在 Active Directory 中建立裝置物件。 這些物件可加入 Azure AD 裝置或網域的 Windows 10 電腦。

| 屬性名稱| 裝置| 註解 |
| --- | :-: | --- |
| altSecurityIdentities | X| |
| 顯示名稱 | X| |
| dn | X| |
| msDS CloudAnchor | X| |
| msDS DeviceID | X| |
| msDS DeviceObjectVersion | X| |
| msDS DeviceOSType | X| |
| msDS DeviceOSVersion | X| |
| msDS DevicePhysicalIDs | X| |
| msDS KeyCredentialLink | X| 僅限與 Windows Server 2016 AD 結構描述 |
| msDS IsCompliant | X| |
| msDS IsEnabled | X| |
| msDS IsManaged | X| |
| msDS RegisteredOwner | X| |


## <a name="notes"></a>備忘稿

- 使用替代的識別碼時, Azure AD 屬性 onPremisesUserPrincipalName 同步處理內部部署屬性 userPrincipalName。 替代 ID 屬性，範例郵件是與 Azure AD 屬性 userPrincipalName 同步處理。
- 在上方清單中的物件類型**使用者**也適用於物件類型**需要**。

## <a name="next-steps"></a>後續步驟
進一步瞭解[Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md)設定。

進一步瞭解[整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)。
