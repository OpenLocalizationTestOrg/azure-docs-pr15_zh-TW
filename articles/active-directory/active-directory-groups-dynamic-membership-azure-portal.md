
<properties
    pageTitle="使用屬性 Azure Active Directory 預覽中建立的群組成員資格的進階的規則 |Microsoft Azure"
    description="如何建立進階的規則動態群組的成員資格包括支援運算式規則運算子和參數。"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules-for-group-membership-in-azure-active-directory-preview"></a>使用屬性 Azure Active Directory 預覽中建立進階的規則的群組成員資格

Azure 入口網站可讓您能夠建立進階的規則，以啟用更複雜屬性型動態成員資格的 Azure Active Directory (Azure AD) 預覽群組。 [什麼是在預覽中？](active-directory-preview-explainer.md) 本文詳細資料的規則屬性和建立這些進階規則的語法。

## <a name="to-create-the-advanced-rule"></a>若要建立進階的規則

1.  [Azure 入口網站](https://portal.azure.com)的目錄是全域管理員帳戶登入。

2.  選取**更多服務**中 [文字] 方塊中，輸入**使用者和群組]** ，然後按下**enter 鍵**。

  ![開啟管理使用者](./media/active-directory-groups-dynamic-membership-azure-portal/search-user-management.png)

3.  在 [**使用者和群組**刀中，選取 [**所有群組**]。

  ![開啟群組刀](./media/active-directory-groups-dynamic-membership-azure-portal/view-groups-blade.png)

4. 在 [**使用者和群組的所有群組**刀中，選取 [**新增**] 命令。

  ![新增群組](./media/active-directory-groups-dynamic-membership-azure-portal/add-group-type.png)

5. 在**群組**刀中，輸入的名稱和新群組的描述。 根據您是否要建立規則的使用者或裝置，選取 [**輸入成員資格**的**動態裝置**或**動態的使用者**，然後選取 [**新增動態查詢**。 用於裝置規則的屬性，請參閱[使用屬性來建立裝置物件的規則](#using-attributes-to-create-rules-for-device-objects)。

  ![新增動態成員資格規則](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

6. **動態成員資格的規則**刀中，在 [**新增動態成員進階的規則**] 方塊中輸入您的規則，按下 enter 鍵，，然後選取**建立**刀底部。

7. 選取 [若要建立群組**群組**刀上的 [**建立**]。

## <a name="constructing-the-body-of-an-advanced-rule"></a>建立進階規則本文

您可以建立的動態群組的成員資格的進階的規則是基本上二進位運算式包含三個部分並產生 true 或 false 的結果。 有三個部分︰

- 左側的參數
- 二進位運算子
- 向右常數

完成的進階的規則看起來就像這樣: (leftParameter binaryOperator 「 RightConstant 」)，其中左、 右括號所需的整個二進位運算式、 雙引號所需的右常數，而左邊的參數的語法是 user.property。 進階的規則最多可以包含一個以上的二進位運算式以-和、-或，以及-不邏輯運算子。

以下是範例正確建構進階規則︰

- (user.department-eq 「 銷售 」)-或 （user.department-eq 「 行銷 」）
- (user.department-eq 「 銷售 」)-和-不 (user.jobTitle-包含 「 SDE 」)

支援的參數和運算式規則運算子的完整清單，請參閱以下各節。 用於裝置規則的屬性，請參閱[使用屬性來建立裝置物件的規則](#using-attributes-to-create-rules-for-device-objects)。

進階規則本文的總長度不能超過 2048 個字元。

> [AZURE.NOTE]
>字串與 regex 作業是不區分大小寫。 您也可以執行 Null 檢查常數，例如使用 $null、 user.department eq $null。
字串包含引號 」 應該使用逸出 」 字元，例如 user.department eq \`「 銷售 」。

## <a name="supported-expression-rule-operators"></a>支援的運算式規則運算子
下表列出所有支援的運算式規則運算子和其語法用於本文的進階規則︰

| 運算子        | 語法         |
|-----------------|----------------|
| 不等於      | -ne            |
| 等於          | -eq            |
| 不開頭 | -notStartsWith |
| 開始使用     | -startsWith    |
| 不包含    | -notContains   |
| 包含        | -包含      |
| 不符合的項目       | -notMatch      |
| 符合的項目           | -符合         |


## <a name="query-error-remediation"></a>查詢錯誤修復
下表列出可能的錯誤，以及如何修正發生

| 查詢分析錯誤     | 錯誤的使用方式       | 更正後的使用方式             |
|-----------------------|-------------------|-----------------------------|
| 錯誤︰ 不支援的屬性。                                      | (user.invalidProperty-eq 「 值 」)       | (user.department-eq 「 值 」)<br/>屬性應該符合從[支援的內容清單](#supported-properties)。                          |
| 錯誤︰ 屬性不支援運算子。                       | (user.accountEnabled-包含 true)                                                                               | (user.accountEnabled-eq true)<br/>屬性是輸入布林值。 使用支援的運算子 (-eq 或-ne) 上從上述清單的布林類型。                                                                                                                                   |
| 錯誤︰ 查詢編譯錯誤。                                      | (user.department-eq 「 銷售 」)-和 (user.department-eq 「 行銷 」)(user.userPrincipalName-match"*@domain.ext") | (user.department-eq 「 銷售 」)-與 （user.department-eq 「 行銷 」）<br/>邏輯運算子應符合從上述支援的內容清單。(user.userPrincipalName-符合".*@domain.ext")or(user.userPrincipalName-符合"@domain.ext$")Error規則運算式中。 |
| 錯誤︰ 二進位運算式不是正確的格式。                     | (user.department – eq 「 銷售 」)(user.department-eq 「 銷售 」)(user.department-eq 「 銷售 」)                             | (user.accountEnabled-eq true)-和 (user.userPrincipalName-包含"alias@domain")<br/>查詢包含多個錯誤。 不在正確的地方中的括號。                                                                                                                            |
| 期間設定動態成員資格發生錯誤︰ 無法辨識的錯誤。 | (user.accountEnabled eq 的 「 True 」 與 user.userPrincipalName-包含"alias@domain")                               | (user.accountEnabled-eq true)-和 (user.userPrincipalName-包含"alias@domain")<br/>查詢包含多個錯誤。 不在正確的地方中的括號。                                                                                                                            |

## <a name="supported-properties"></a>支援的屬性
以下是您可以使用進階規則中的所有使用者屬性︰

### <a name="properties-of-type-boolean"></a>屬性，請輸入布林值

允許的運算子

* -eq


* -ne


| 屬性     | 允許的值  | 使用方式                          |
|----------------|-----------------|--------------------------------|
| accountEnabled | false，則為 true      | user.accountEnabled-eq true)  |
| dirSyncEnabled | 為 true，則為 false null | (user.dirSyncEnabled-eq true) |

### <a name="properties-of-type-string"></a>字串類型的屬性

允許的運算子

* -eq


* -ne


* -notStartsWith


* -StartsWith


* -包含


* -notContains


* -符合


* -notMatch

| 屬性                 | 允許的值                                                                                        | 使用方式                                                     |
|----------------------------|-------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| 縣/市                       | 任何字串值或 $null                                                                           | (user.city-eq 「 值 」)                                   |
| 國家/地區                    | 任何字串值或 $null                                                                            | (user.country-eq 「 值 」)                                |
| 部門                 | 任何字串值或 $null                                                                          | (user.department-eq 「 值 」)                             |
| 顯示名稱                | 任何字串值                                                                                 | (user.displayName-eq 「 值 」)                            |
| facsimileTelephoneNumber   | 任何字串值或 $null                                                                           | (user.facsimileTelephoneNumber-eq 「 值 」)               |
| givenName                  | 任何字串值或 $null                                                                           | (user.givenName-eq 「 值 」)                              |
| jobTitle                   | 任何字串值或 $null                                                                           | (user.jobTitle-eq 「 值 」)                               |
| 郵件                       | 任何字串值或 $null （SMTP 位址的使用者）                                                  | (user.mail-eq 「 值 」)                                   |
| mailNickName               | 任何字串值 （使用者的郵件別名）                                                            | (user.mailNickName-eq 「 值 」)                           |
| 行動裝置                     | 任何字串值或 $null                                                                           | (user.mobile-eq 「 值 」)                                 |
| objectId                   | 使用者物件的 GUID                                                                            | (user.objectId-eq 」 1111111-1111年-1111年-1111年-111111111111 」) |
| passwordPolicies           | 無 DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration、 DisableStrongPassword |   (user.passwordPolicies-eq 「 DisableStrongPassword 」)                                                      |
| physicalDeliveryOfficeName | 任何字串值或 $null                                                                            | (user.physicalDeliveryOfficeName-eq 「 值 」)             |
| 郵遞區號                 | 任何字串值或 $null                                                                            | (user.postalCode-eq 「 值 」)                             |
| preferredLanguage          | ISO 639-1 程式碼                                                                                        | (user.preferredLanguage-eq 「 EN-US 」)                      |
| sipProxyAddress            | 任何字串值或 $null                                                                            | (user.sipProxyAddress-eq 「 值 」)                        |
| 狀態                      | 任何字串值或 $null                                                                            | (user.state-eq 「 值 」)                                  |
| streetAddress              | 任何字串值或 $null                                                                            | (user.streetAddress-eq 「 值 」)                          |
| 姓氏                    | 任何字串值或 $null                                                                            | (user.surname-eq 「 值 」)                                |
| telephoneNumber            | 任何字串值或 $null                                                                            | (user.telephoneNumber-eq 「 值 」)                        |
| usageLocation              | 兩個字母的國家/地區碼                                                                           | (user.usageLocation-eq 「 美國 」)                             |
| userPrincipalName          | 任何字串值                                                                                     | (user.userPrincipalName eq"alias@domain")               |
| userType                   | 成員來賓 $null                                                                                    | (user.userType-eq 「 成員 」)                              |

### <a name="properties-of-type-string-collection"></a>類型字串集合內容

允許的運算子

* -包含


* -notContains

| Poperties      | 允許的值                        | 使用方式                                                |
|----------------|---------------------------------------|------------------------------------------------------|
| otherMails     | 任何字串值                      | (user.otherMails-包含"alias@domain")           |
| proxyAddresses | SMTP: alias@domain smtp:alias@domain | (user.proxyAddresses-包含 「 SMTP:alias@domain") |

## <a name="extension-attributes-and-custom-attributes"></a>擴充屬性和自訂屬性
動態成員資格的規則支援擴充屬性和自訂屬性。

擴充屬性從內部部署視窗伺服器 AD 同步處理，而讓 「 ExtensionAttributeX 」，其中 X 等於 1-15 的格式。
使用副檔名屬性的規則的範例

（user.extensionAttribute15 eq 「 行銷 」）

自訂屬性會同步處理從內部部署 Windows Server AD 或從連線的 SaaS 應用程式和的格式 」 user.extension_[GUID]\__ [屬性] 」，其中 [GUID] AAD 中建立屬性的應用程式中 AAD 的唯一識別碼，而 [屬性] 會屬性的名稱為其建立。
使用自訂屬性的規則的範例

user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  

可以藉由查詢使用者目錄中找到的自訂屬性名稱的屬性使用圖形檔案總管和搜尋屬性名稱。

## <a name="direct-reports-rule"></a>直屬規則
您現在可以填入根據使用者的管理員屬性群組的成員。

**若要將群組設定為 [管理員] 群組**

1. 請依照下列步驟 1-5 中[建立進階的規則](#to-create-the-advanced-rule)]，然後選取**成員資格類型**的**動態使用者**。

2. 在**動態成員資格的規則**刀中，輸入下列語法規則︰

    將報表的*直屬員工的 {obectID_of_manager}*。 直屬員工的有效規則的範例

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    「 62e19b97-8b3d-4d4a-a106-4ce66896a863 」 在哪裡 objectID 的管理員]。 在 [使用者] 頁面之管理員的使用者**設定檔] 索引標籤**上 Azure AD 中找物件識別碼。

3. 儲存此規則，符合規則的所有使用者會都加入為成員的群組。 可能需要一些群組，一開始填入分鐘。


## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用屬性來建立裝置物件的規則

您也可以建立一個規則，會選取群組中的成員資格的裝置物件。 可以使用下列裝置屬性︰

| 屬性           | 允許的值                  | 使用方式                                                |
|----------------------|---------------------------------|------------------------------------------------------|
| 顯示名稱          | 任何字串值                | (device.displayName-eq"葉 Iphone 」)                 |
| deviceOSType         | 任何字串值                | (device.deviceOSType-eq 」 IOS 」)                      |
| deviceOSVersion      | 任何字串值                | （裝置。OSVersion eq 」 9.1 」)                         |
| isDirSynced          | 為 true，則為 false null                 | (device.isDirSynced-eq 「 true 」)                      |
| isManaged            | 為 true，則為 false null                 | (device.isManaged-eq"false")                       |
| isCompliant          | 為 true，則為 false null                 | (device.isCompliant-eq 「 true 」)                      |


## <a name="additional-information"></a>其他資訊
這些文章提供 Azure Active Directory 中群組的其他資訊。

* [請參閱現有的群組](active-directory-groups-view-azure-portal.md)
* [建立新的群組，並新增成員](active-directory-groups-create-azure-portal.md)
* [管理群組的設定](active-directory-groups-settings-azure-portal.md)
* [管理群組的成員資格](active-directory-groups-membership-azure-portal.md)
* [管理動態規則的群組中的使用者](active-directory-groups-dynamic-membership-azure-portal.md)
