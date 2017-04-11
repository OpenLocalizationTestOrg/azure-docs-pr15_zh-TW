<properties
   pageTitle="Azure Active Directory 稽核報告事件 |Microsoft Azure"
   description="稽核事件可供檢視和下載您的 Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/19/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-audit-report-events"></a>Azure Active Directory 稽核報告的事件

*這份文件是 [Azure Active Directory 報告指南] (使用中-目錄-報告-guide.md) 的一部分。*

Azure Active Directory 稽核報告可協助您識別有權限的動作發生在他們的 Azure Active Directory 中的客戶。 有權限的動作包括提高權限 （例如，角色建立或變更密碼重設）、 變更原則設定 （例如 [密碼原則]） 或變更目錄設定 （例如，網域同盟設定變更）。 報告的事件] 名稱的 [動作項目執行動作，受到變更，以及日期和時間 （UTC) 的目標資源提供的稽核記錄。 客戶可透過[Azure 入口網站](https://portal.azure.com/)中，其 Azure Active Directory 擷取的稽核事件清單[檢視稽核記錄](active-directory-reporting-azure-portal.md)中所述。


## <a name="list-of-audit-report-events"></a>稽核報告的事件清單
<!--- audit event descriptions should be in the past tense --->

事件                               | 事件的描述
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**使用者事件**                      |
新增使用者                             | 新增使用者至目錄。
刪除使用者                          | 從目錄中刪除的使用者。
設定授權屬性               | 設定目錄中的使用者授權的屬性。
重設使用者密碼                  | 重設使用者的目錄中的密碼。
變更使用者的密碼                 | 變更目錄中的使用者的密碼。
變更使用者的授權                  | 變更目錄中的使用者指派的授權。 若要查看更新授權，查看下列[更新使用者](#update-user-attributes)屬性
更新使用者                          | 更新目錄中的使用者。 [請參閱下面的](#update-user-attributes)可更新的屬性。
設定強制變更使用者的密碼       | 強制使用者變更其密碼，登入的屬性設定。
更新使用者認證        | 使用者變更的密碼  
**群組事件**                     |
新增群組                            | 建立目錄中的群組。
更新群組                         | 更新目錄中的群組。 若要查看哪些群組已更新內容，請參閱下方的一節中的 [[群組屬性稽核](#update-group-attributes)
刪除群組                         | 從目錄中刪除群組。
將成員新增至群組            | 新增目錄中群組的成員。
從群組移除成員         | 移除目錄中的群組成員。
CreateGroupSettings              | 建立的群組設定
UpdateGroupSettings          | 更新群組設定。 若要查看哪些群組設定更新，請參閱下方的一節中的 [[群組屬性稽核](#update-group-attributes)
DeleteGroupSettings            | 刪除的群組設定
SetGroupLicense                  | 設定] 群組中的授權。
SetGroupManagedBy              | 設定管理使用者的群組
AddGroupMember               | 新增的成員至群組
RemoveGroupMember            | 從群組移除成員
AddGroupOwner                | 加入至群組的擁有者
RemoveGroupOwner                 | 從群組移除擁有者
**應用程式的事件**               |
新增服務原則                | 新增服務主要的目錄。
移除服務原則             | 移除目錄服務主要。
新增服務本金認證    | 新增至服務主要的認證。
移除服務本金認證 | 移除本金服務的認證。
新增委派的項目                 | 在目錄中建立[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) 。
設定委派的項目                 | 更新目錄中[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) 。
移除委派的項目              | 刪除目錄中[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permissiongrant-entity) 。
**角色事件**                      |
將角色成員新增至角色              | 新增目錄的角色的使用者。
移除角色的成員角色         | 移除目錄的角色的使用者。
設定公司連絡資訊      | 設定公司層級連絡人偏好設定。 這包含行銷，以及技術的通知，Microsoft 線上服務的相關的電子郵件地址。
新增委派的項目                 | 在目錄中建立[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) 。
設定委派的項目                 | 更新目錄中[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) 。
移除委派的項目              | 刪除目錄中[OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) 。
AddSevicePrincipalOwner          | 加入至服務主要的擁有者。
RemoveSevicePrincipalOwner   | 移除服務主要的擁有者。
AddApplication  | 新增應用程式。
UpdateApplication | 更新應用程式。 若要查看哪些應用程式設定更新，請參閱下方的一節中的 [[應用程式屬性稽核](#update-application-attributes)
DeleteApplication | 刪除應用程式。
RestoreApplication|還原應用程式。
AddApplicationOwner   |     將應用程式中的擁有者。
RemoveApplicationOwner    |     從應用程式移除擁有者。
**角色事件**                         |
將角色成員新增至角色                 | 新增目錄的角色的使用者。
移除角色的成員角色            | 移除目錄的角色的使用者。
AddRoleDefinition               | 新增的角色定義。
UpdateRoleDefinition                | 更新角色定義。 若要查看哪些角色設定更新，請參閱下方的一節中的 [[稽核的角色定義屬性](#update-role-definition-attributes)
DeleteRoleDefinition                | 刪除角色定義。
AddRoleAssignmentToRoleDefinition | 新增的角色指派到角色定義。
RemoveRoleAssignmentFromRoleDefinition | 從角色定義移除的角色指派。
AddRoleFromTemplate     | 從範本新增的角色。
UpdateRole  | 更新的角色。
AddRoleScopeMemberToRole    | 新增至角色的範圍的成員。
RemoveRoleScopedMemberFromRole  | 移除範圍的成員角色。
**裝置事件 （所有新事件）**                    |
AddDevice               | 新增的裝置。
UpdateDevice            | 更新的裝置。 若要查看哪些裝置已更新內容，請參閱下方的一節中的[裝置內容 Audited](#update-device-attributes)
DeleteDevice            | 已刪除的裝置。
AddDeviceConfiguration      | 新增的裝置設定。
UpdateDeviceConfiguration   | 更新的裝置設定。 若要查看哪些裝置設定已更新內容，請參閱下方的一節中的[裝置設定內容 Audited](#update-device-configuration-attributes)
DeleteDeviceConfiguration   | 刪除的裝置設定。
AddRegisteredOwner     | 新增至裝置註冊擁有人。
AddRegisteredUsers     | 加入裝置註冊的使用者。
RemoveRegisteredOwner    | 從裝置移除已註冊的擁有者。
RemoveRegisteredUsers    | 從裝置移除註冊的使用者。
RemoveDeviceCredentials  | 移除裝置認證。
**B2B 事件**                       |
批次邀請上傳。              | 系統管理員已上傳的檔案包含要傳送至協力廠商使用者的邀請。
批次處理的邀請。             | 已處理檔案包含合作夥伴使用者的邀請。
邀請外部使用者。                | 外部使用者受邀的目錄。
兌換外部使用者邀請]。         | 外部使用者已被兌換其目錄的邀請。
將外部使用者新增至群組。          | 外部使用者已指派的成員資格目錄中的群組。
將外部使用者指派的應用程式。 | 外部使用者已指派直接存取應用程式。
病毒租用戶建立。               | 邀請贖回已 Azure AD 中建立新的租用戶類型。
病毒使用者建立。                 | 使用者在現有的租用戶中建立 Azure AD 來邀請贖回。
**管理單位 （所有新事件）**             |
AddAdministrativeUnit   |   新增系統管理單位。
UpdateAdministrativeUnit|   更新管理單位。 若要查看哪些管理單位已更新內容，請參閱下方的一節中的 [[系統管理單位內容稽核](#update-administrative-unit-attributes)
DeleteAdministrativeUnit|   刪除管理單位。
AddMemberToAdministrativeUnit|  成員新增至 [系統管理單位。
RemoveMemberFromAdministrativeUnit|     管理單元中移除成員。
**目錄事件**                 |
將合作夥伴新增至公司               | 新增目錄的合作夥伴。
移除公司的合作夥伴          | 從目錄中移除合作夥伴。
DemotePartner   |   降階合作夥伴。
將網域新增至公司                | 新增網域至目錄。
移除公司網域           | 從目錄中移除網域。
更新的網域                        | 更新目錄上的網域。 若要查看哪些網域已更新內容，請參閱下方的一節中的 [[網域內容稽核](#update-domain-attributes)
設定網域驗證            | 變更公司的預設網域設定。
設定公司連絡資訊      | 設定公司層級連絡人偏好設定。 這包含行銷，以及技術的通知，Microsoft 線上服務的相關的電子郵件地址。
在網域設定同盟    | 更新網域的同盟設定。
驗證網域                        | 驗證網域的目錄。
確認電子郵件驗證的網域         | 驗證網域上使用電子郵件驗證的目錄。
在 [公司設定 DirSyncEnabled 旗標   | Azure AD Sync 內啟用目錄的屬性設定。
設定密碼原則                  | 設定使用者密碼長度與字元的限制式。
設定公司資訊              | 更新公司層級的資訊。 請參閱[Get-msolcompanyinformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell cmdlet，如需詳細資訊。
SetCompanyAllowedDataLocation  |    設定公司允許資料的位置。
SetCompanyDirSyncEnabled    |   設定 DirSyncEnabled 旗標。
SetCompanyDirSyncFeature |  設定目錄同步功能。
SetCompanyInformation |   設定公司資訊。
SetCompanyMultiNationalEnabled    |     設定公司啟用多語系功能。
SetDirectoryFeatureOnTenant   |     設定租用戶的目錄功能。
SetTenantLicenseProperties  |   設定租用戶授權屬性。
CreateCompanySettings   |   建立公司設定
UpdateCompanySettings   |   更新公司設定。 若要查看哪些公司已更新內容，請參閱下方的一節中的 [[稽核公司屬性](#update-company-attributes)
DeleteCompanySettings   |   刪除公司設定
SetAccidentalDeletionThreshold   |  設定不慎刪除閥值。
SetRightsManagementProperties   |   設定權限管理內容。
PurgeRightsManagementProperties     |   清除 [權限管理內容。
UpdateExternalSecrets   |   更新外部機密資料
**原則事件 （所有新事件）**           |
AddPolicy   |   新增原則。
UpdatePolicy    |   更新原則。
DeletePolicy    |   刪除原則。
AddDefaultPolicyApplication     |   將原則新增到應用程式。
AddDefaultPolicyServicePrincipal    |   新增原則到服務主要。
RemoveDefaultPolicyApplication  |   移除應用程式原則。
RemoveDefaultPolicyServicePrincipal     |   移除服務主要原則。
RemovePolicyCredentials     |   移除原則認證。

## <a name="audit-report-retention"></a>稽核報告保留
Azure AD 稽核報告的事件會保留 180 天。 如需更多有關報表保留的詳細資訊，請參閱[Azure Active Directory 報表保留原則](active-directory-reporting-retention.md)。

客戶想保留長期儲存其稽核的事件，報告 API 可以用於定期傳送到不同的資料存放區的 [稽核的事件。 如需詳細資訊，請參閱[快速入門報告 API](active-directory-reporting-api-getting-started.md) 。

## <a name="properties-included-with-each-audit-event"></a>包含在每個稽核事件的內容

屬性      | 描述
------------- | --------------------------------------------------------------
日期及時間 | 稽核事件發生的時間與日期
動作項目         | 執行動作的服務主要或使用者
巨集指令        | 已執行的動作
目標        | 使用者或服務主要上所執行的動作


## <a name="update-user-attributes"></a>「 更新使用者 」 屬性
「 更新使用者 」 稽核事件包含哪些使用者屬性已更新的其他資訊。 每個屬性，同時在前一個與新的值是包含。

屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                      | 使用者可以登入。
AssignedLicense                     | 所有已指派給使用者的授權。
AssignedPlan                        | 指派給使用者的授權所產生的服務方案。
LicenseAssignmentDetail             | 授權指派給使用者的詳細資料。 比方說，如果包含群組為基礎的授權，這包括授與授權的群組。
行動電話                              | 使用者的行動電話。
OtherMail                           | 使用者的備用電子郵件地址。
OtherMobile                         | 使用者的替代行動電話。
StrongAuthenticationMethod          | 設定多重因素驗證，例如語音通話、 簡訊或驗證的程式碼，從行動應用程式使用者驗證方法的清單。
StrongAuthenticationRequirement     | 如果在執行多因素驗證時，啟用或停用此使用者。
StrongAuthenticationUserDetails     | 使用者的電話號碼，替代電話號碼和電子郵件地址用於多重因素驗證和密碼重設驗證。
StrongAuthenticationPhoneAppDetail  | 執行 2FA 登入您註冊的詳細資料 forof 手機應用程式
TelephoneNumber                     | 使用者的電話號碼。
AlternativeSecurityId               | 其他安全性識別碼的物件。
CreationType                        |建立方式 （無論是邀請或病毒） 的使用者。
InviteTicket                        |使用者的邀請票證清單。
InviteReplyUrl                      |若要回覆接受邀請時的 url 的清單。
InviteResources                     |使用者收到的資源的清單。
LastDirSyncTime                     |由於從授權同步處理更新物件的最後一次 （客戶，內部部署） 的目錄。
MSExchRemoteRecipientType           |對應至 MSO 收件者類型。 指向 [MSO 收件者類型] https://msdn.microsoft.com/library/microsoft.office.interop.outlook.recipient.type.aspx 收件者的類型
PreferredDataLocation               |想要的位置使用者、 群組、 連絡人、 公用資料夾或裝置的資料。
ProxyAddresses                      |Exchange Server 收件者物件辨識外部郵件系統中的地址。
StsRefreshTokensValidFrom           |會重新整理 token 撤銷資訊-之前這次會被視為發出任何 STS 重新整理權杖過期。
UserPrincipalName                   |網際網路樣式登入名稱，為使用者的 UPN。
UserState                           |使用者 PendingApproval/PendingAcceptance/接受/PendingVerification 狀態。
UserStateChangedOn                  |UserState 上一次變更的時間戳記。 用來觸發生命週期工作流程。
UserType                            |使用者類型。 成員 (0)，來賓 (1)，Viral(2)。

## <a name="update-group-attributes"></a>「 更新群組 」 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
分類            | 整合群組 （HBI、 MBI 等） 分類。
描述               | 閱讀物件的相關描述性片語。  
顯示名稱               |顯示名稱] 的物件
DirSyncEnabled            |指出是否從授權會發生同步處理 （客戶，內部部署） 的目錄。
GroupLicenseAssignment    |授權的指派的群組
GroupType                 |群組類型，整合 (0)
IsMembershipRuleLocked    |指出 MembershipRule 屬性由自助群組管理服務所設定，且無法變更的使用者。 只適用於 GroupType，包含 GroupType.DynamicMembership 的群組
IsPublic                  |表示是否群組為公用/私人旗標。
LastDirSyncTime           |上次更新物件當做從授權同步處理 （內部部署客戶） 的目錄。
郵件                      |主要的電子郵件地址
MailEnabled               |會指出此群組是否具有電子郵件功能。
MailNickname              |通訊錄物件的 moniker，通常是電子郵件名稱前面的部分"@"符號。   
MembershipRule            |字串，表示使用自助群組管理服務來決定哪些成員應該指派給此群組的準則。 另請參閱 IsMembershipRuleLocked。 只適用於 GroupType，包含 GroupType.DynamicMembership 的群組。
MembershipRuleProcessingState| 定義的處理此群組的成員資格狀態的自助群組管理服務來定義列舉值。 只適用於 GroupType，包含 GroupType.DynamicMembership 的群組。
ProxyAddresses            |Exchange Server 收件者物件辨識外部郵件系統中的地址。
RenewedDateTime           |時間戳記的群組已最近更新的記錄。   
SecurityEnabled           |指出是否群組的成員資格可能會影響授權決策。
WellKnownObject           |標記目錄物件，將其指定為其中一個預先定義的設定。

## <a name="update-device-attributes"></a>「 更新裝置 」 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled | 指出是否可以驗證安全性原則。
CloudAccountEnabled | 指出是否可以驗證安全性原則。 當裝置內部部署掌握撰寫 InTune。
CloudDeviceOSType | 裝置類型根據 OS 例如 Windows RT，iOS。 設定以雲端服務 （例如 Intune) 時，這個屬性會變成代表性目錄中的 DeviceOSType。
CloudDeviceOSVersion | 版本的作業系統。 設定以雲端服務 （例如 Intune) 時，這個屬性會變成代表性目錄中的 DeviceOSVersion。
CloudDisplayName | 顯示名稱 LDAP 屬性的值。 設定以雲端服務 （例如 Intune) 時，這個屬性會變成顯示名稱的目錄中的授權。
CloudCreated |指出是否雲端服務來建立物件。
CompliantUntil | 哪些之前的裝置已被視為相容。
DeviceMetadata |自訂裝置的中繼資料
DeviceObjectVersion | 這個屬性會用來識別裝置的結構描述版本。
DeviceOSType |裝置類型根據 OS 例如 Windows RT，iOS。 撰寫註冊服務及要更新的 MDM 管理服務或 STS 淺管理服務。
DeviceOSVersion |版本的作業系統。 撰寫註冊服務及要更新的 MDM 管理服務或 STS 淺管理服務。
DevicePhysicalIds |多重值的屬性，目的是儲存實體裝置的識別碼。 這可能包括 BIOS 識別碼，TPM 憑證碼、 硬體等特定的識別碼。
DirSyncEnabled | 指出是否從授權 （客戶，內部部署） 發生同步處理的目錄。
顯示名稱 |顯示名稱] 的物件
IsCompliant | 這個屬性會用來管理行動裝置管理狀態的裝置。
IsManaged |這個屬性用來表示雲端 MDM 裝置管理
LastDirSyncTime |由於從授權同步處理更新物件的最後一次 （內部部署客戶） 的目錄。

## <a name="update-device-configuration-attributes"></a>「 更新裝置設定] 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
MaximumRegistrationInactivityPeriod |移除視為裝置可以停用前最大天數。
RegistrationQuota   |用來限制的可容許的單一使用者的裝置註冊數目的原則。

## <a name="update-service-principal-configuration-attributes"></a>「 更新服務原則設定 「 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled |指出是否可以驗證安全性原則。
AppPrincipalId | 外部應用程式定義身分識別的安全性原則。
顯示名稱 |顯示名稱] 的物件
ServicePrincipalName    | 服務主體名稱，其中包含 「 名稱/授權 」 位置名稱指定應用程式類別值，並授權至少包含主機名稱 [: 連接埠] 或 「 名稱 」，指定的服務主要的識別碼。

## <a name="update-app-attributes"></a>「 應用程式更新 」 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |地址 （重新導向 Url） 指派給服務主要組。
AppId                               |應用程式的應用程式識別碼   
AppIdentifierUri | 應用程式 URI，識別應用程式。  通常是應用程式存取 URL。
AppLogoUrl |儲存在 cdn 到底應用程式標誌圖像的 url。
AvailableToOtherTenants | 應用程式，則為 true 多租用戶應用程式 （亦即就能使用其他租用戶）。
顯示名稱 | 顯示名稱] 的 [應用程式名稱
授權 |應用程式權利的清單。
ExternalUserAccountDelegationsAllowed |旗標表示資源應用程式是否受信任的項目，並可以建立的外部使用者帳戶的委派項目。
GroupMembershipClaims |群組的成員資格宣告原則。
PublicClient | 如果用戶端無法保密則為 true （亦即 OAuth2.0 中的非機密用戶端）
RecordConsentConditions | 同意條件，所定義的合約字詞的類型︰ 無 (0)，SilentConsentForPartnerManagedApp(1)。 此值將公開圖形 API 結構描述中，才可以設定/變更租用戶管理員。
RequiredResourceAccess |RequiredResourceAccess 屬性的值的 XML 內容。
WebApp |如果為 true，則表示此應用程式的 web 應用程式。
WwwHomepage |主要的網頁。

## <a name="update-role-attributes"></a>「 更新角色] 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AppAddress |地址 （重新導向 Url） 指派給服務主要組。
BelongsToFirstLoginObjectSet |如果為 true，表示這個物件屬於一組啟用的第一個新的租用戶管理員的登入所需的物件。
內建 |指出是否擁有系統物件的存留時間。
描述 | 閱讀物件的相關描述性片語。  
顯示名稱 |顯示名稱] 的物件
MailNickname | 通訊錄物件的 moniker，通常是電子郵件名稱前面的部分"@"符號。
RoleDisabled |指出是否應該忽略的存取檢查的角色。
RoleTemplateId | 角色範本的身分識別。
ServiceInfo |服務-佈建的特定資訊可能會由 MOAC 及/或其他服務的執行個體 （相同或不同的服務類型）。
TaskSetScopeReference |識別 TaskSet 及一組的角色或 RoleTemplate 相關聯的範圍。
ValidationError |發佈同盟服務描述關於屬性或連結的物件管理員動作來解決非暫時性、 服務特定錯誤的資訊。
WellKnownObject |標記目錄物件，將其指定為其中一個預先定義的設定。

## <a name="update-role-definition-attributes"></a>「 更新角色定義 」 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AssignableScopes    |授權範圍的安全性原則指派此 RoleDefinition 時可以參考的集合。
顯示名稱     |顯示名稱] 的物件
GrantedPermissions  |此 RoleDefinition 授與權限。


## <a name="update-administrative-unit-attributes"></a>「 更新管理單位] 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
描述 |   當您變更管理單位的描述，這個屬性會更新。
顯示名稱 |   當您變更管理單位的名稱，這個屬性會更新。

## <a name="update-company-attributes"></a>「 更新公司 」 屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
AllowedDataLocation     | 在其中的公司允許使用者佈建的位置。
AuthorizedServiceInstance   | 要部署計劃可能的服務執行個體的名稱。
DirSyncEnabled |指出是否從授權 （客戶，內部部署） 發生同步處理的目錄。
DirSyncStatus |指出是否地址活頁簿中的物件此租用戶內容的同步處理發生的授權 （客戶，內部部署） 從目錄。擴充的公司物件的 DirSyncEnabled 屬性中。
DirSyncFeatures |若要追蹤的一組啟用及停用 dirsync 功能租用戶位元旗標。
DirectoryFeatures |啟用或停用目錄功能。
DirSyncConfiguration |包含目前的租用戶專屬的所有 DirSync 設定。
顯示名稱 |顯示名稱] 的物件
IsMnc |布林值旗標設定為"true"iff 公司已啟用多語系公司功能。
ObjectSettings | 設定適用於物件的範圍的集合。
PartnerCommerceUrl |合作夥伴的商務網站的 URL。
PartnerHelpUrl |合作夥伴說明網站的 URL。
PartnerSupportEmail | 合作夥伴的支援電子郵件的 URL。
PartnerSupportTelephone |合作夥伴的支援電話 URL。
PartnerSupportUrl |合作夥伴的支援網站的 URL。
StrongAuthenticationDetails |StrongAuthentication 相關詳細資料。
StrongAuthenticationPolicy |公司的強式驗證原則。
TechnicalNotificationMail |通知與公司相關的技術問題的電子郵件地址。
TelephoneNumber |遵守 ITU 建議 E.123 的電話號碼。
TenantType |租用戶類型。 如果未指定此值，租用戶是公司。 可能的值，否則就是︰ MicrosoftSupport (0)、 SyndicatePartner (1)、 BreadthPartner (2) BreadthPartnerDelegatedAdmin (3) (4) ResellerPartnerDelegatedAdmin ValueAddedResellerPartnerDelegatedAdmin (5)。
VerifiedDomain |繫結至公司 DNS 網域名稱的一組。

## <a name="update-domain-attributes"></a>「 更新的網域 」 的屬性
屬性                           | 描述
-------------------------------     | -------------------------------------------------------------------------------------------------------------------------------------------------------
功能    |如果有的話，位元描述的網域，功能旗標。
預設值 |會指出網域是否為預設值。例如，預設 UserPrincipalName 後置字元系統管理員在 MOAC 中建立新的使用者。
初始 |指出是否網域初始網域的公司，如下所 OCP 配置。 初始網域是唯一的子網域的 Microsoft Online 網域;e.g.contoso3.microsoftonline.com。
LiveType    |對應 Windows Live 的命名空間，如果有任何類型。
名稱    | 端點的識別項。
PasswordNotificationWindowDays  |通知的密碼到期使用者之前的天數。
PasswordValidityPeriodDays  | 必須變更密碼很適合前的天數。

稽核記錄是必要的許多法規遵循法規控制項。 使用以符合其符合法規的 Azure Active Directory 稽核報告的客戶，建議您的客戶送出的本說明主題可協助說明的報表詳細資料的客戶的匯出的稽核報告的複本。 如果稽核想要了解 Azure 目前符合法規遵循法規，直接至 Microsoft Azure 信任中心的[規範頁面](https://azure.microsoft.com/support/trust-center/compliance/)稽核。
