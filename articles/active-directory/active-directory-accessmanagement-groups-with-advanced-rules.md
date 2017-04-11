
<properties
    pageTitle="若要建立進階的規則使用屬性 |Microsoft Azure"
    description="如何-的建立進階的規則的群組，包括支援運算式規則運算子和參數。"
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
    ms.date="08/15/2016"
    ms.author="curtand"/>


# <a name="using-attributes-to-create-advanced-rules"></a>若要建立進階的規則使用屬性

Azure 傳統入口網站可讓您能夠建立進階的規則，以啟用更複雜屬性型動態成員資格的 Azure Active Directory (Azure AD) 群組。  

當使用者變更的任何屬性，系統會評估，查看使用者的屬性變更是否會觸發任何群組的目錄中的所有動態群組規則新增或移除。 如果使用者 just 群組中的規則，它們會為成員新增至該群組。 不再符合其所屬的群組的規則，它們會從該群組移除成員。

## <a name="to-create-the-advanced-rule"></a>若要建立進階的規則

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)中，選取 [ **Active Directory**中，然後再開啟您組織的目錄。

2. 選取 [**群組**] 索引標籤，然後開啟 [您要編輯的群組。

3. 選取 [**設定**] 索引標籤，選取 [**進階的規則**] 選項中，，然後在 [文字] 方塊中輸入進階的規則。

## <a name="constructing-the-body-of-an-advanced-rule"></a>建立進階規則本文

您可以建立的動態群組的成員資格的進階的規則是基本上二進位運算式包含三個部分並產生 true 或 false 的結果。 有三個部分︰

- 左側的參數
- 二進位運算子
- 向右常數

完成的進階的規則看起來就像這樣: (leftParameter binaryOperator 「 RightConstant 」)，其中左、 右括號所需的整個二進位運算式、 雙引號所需的右常數，而左邊的參數的語法是 user.property。 進階的規則最多可以包含一個以上的二進位運算式以-和、-或，以及-不邏輯運算子。
以下是範例正確建構進階規則︰

- (user.department-eq 「 銷售 」)-或 （user.department-eq 「 行銷 」）
- (user.department-eq 「 銷售 」)-和-不 (user.jobTitle-包含 「 SDE 」)

支援的參數和運算式規則運算子的完整清單，請參閱以下各節。

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

1. 在 Azure 傳統的入口網站中，按一下 [ **Active Directory**，然後按一下貴組織的目錄的名稱。

2. 選取 [**群組**] 索引標籤，然後開啟 [您要編輯的群組。

3. 選取 [**設定**] 索引標籤，然後選取 [**進階規則**。

4. 輸入下列語法規則︰

    將報表的*直屬員工的 {obectID_of_manager}*。 直屬員工的有效規則的範例

                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863”

    「 62e19b97-8b3d-4d4a-a106-4ce66896a863 」 在哪裡 objectID 的管理員]。 在 [使用者] 頁面之管理員的使用者**設定檔] 索引標籤**上 Azure AD 中找物件識別碼。

3. 儲存此規則，符合規則的所有使用者會都加入為成員的群組。 可能需要一些群組，一開始填入分鐘。


## <a name="using-attributes-to-create-rules-for-device-objects"></a>使用屬性來建立裝置物件的規則

您也可以建立一個規則，會選取群組中的成員資格的裝置物件。 可以使用下列裝置屬性︰

| 屬性              | 允許的值                  | 使用方式                                                       |
|-------------------------|---------------------------------|-------------------------------------------------------------|
| 顯示名稱             | 任何字串值                | (device.displayName-eq"葉 Iphone 」)                       |
| deviceOSType            | 任何字串值                | (device.deviceOSType-eq 」 IOS 」)                             |
| deviceOSVersion         | 任何字串值                | （裝置。OSVersion eq 」 9.1 」)                                |
| isDirSynced             | 為 true，則為 false null                 | (device.isDirSynced-eq 「 true 」)                             |
| isManaged               | 為 true，則為 false null                 | (device.isManaged-eq"false")                              |
| isCompliant             | 為 true，則為 false null                 | (device.isCompliant-eq 「 true 」)                             |
| deviceCategory          | 任何字串值                | (device.deviceCategory eq 」 」)                              |
| deviceManufacturer      | 任何字串值                | (device.deviceManufacturer-eq"Microsoft")                 |
| deviceModel             | 任何字串值                | (device.deviceModel-eq 」 IPhone 7 + 」)                        |
| deviceOwnership         | 任何字串值                | (device.deviceOwnership eq 」 」)                             |
| 網域名稱              | 任何字串值                | (device.domainName-eq 」 contoso.com 」)                       |
| enrollmentProfileName   | 任何字串值                | (device.enrollmentProfileName eq 」 」)                       |
| isRooted                | 為 true，則為 false null                 | (device.deviceOSType-eq 「 true 」)                            |
| managementType          | 任何字串值                | (device.managementType eq 」 」)                              |
| organizationalUnit      | 任何字串值                | (device.organizationalUnit eq 」 」)                          |
| deviceId                | 有效的 deviceId                | (device.deviceId-eq 「 d4fe7726-5966-431c-b3b8-cddc8fdb717d 」 |

> [AZURE.NOTE]
> 無法使用 [簡易規則] 下拉式清單中 Azure 傳統入口網站建立這些裝置的規則。


## <a name="additional-information"></a>其他資訊
這些文章提供 Azure Active Directory 的其他資訊。

* [疑難排解動態群組的成員資格](active-directory-accessmanagement-troubleshooting.md)

* [管理資源的存取權與 Azure Active Directory 群組](active-directory-manage-groups.md)

* [Azure Active Directory cmdlet 設定群組設定](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Azure Active Directory 中的應用程式管理文件索引](active-directory-apps-index.md)

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
