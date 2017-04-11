<properties
    pageTitle="Azure Active Directory cmdlet 設定群組設定 |Microsoft Azure"
    description="如何管理群組使用 Azure Active Directory cmdlet 的設定。"
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
    ms.date="09/22/2016"
    ms.author="curtand"/>


# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory cmdlet 設定群組設定

在您的目錄，可以設定整合群組的下列設定︰

1.  分類: 逗點分隔清單的使用者可以設定群組的分類。 範例是 「 分類 」、 「 機密 」 和 「 最高機密 」。

2.  使用準則 URL: URL 所定義貴組織的使用者指向使用整合群組]，使用規定。 在使用者介面，使用者使用的群組會顯示這個 URL。

3.  群組建立已啟用︰ 是否無，允許部分或所有使用者建立整合的群組。 若設定為在所有使用者可以都建立群組。 設定為 [關閉時，沒有任何使用者可以建立群組。 當關閉，您也可以指定其仍允許建立的使用者群組的安全性群組。

這些設定會使用設定來設定和 SettingsTemplate 物件。 開始時，您不會看到任何設定物件在您的目錄。 這表示您的目錄設定的預設設定。 若要變更預設設定，您會建立新範本設定的設定物件。 設定範本是由 Microsoft 所定義。

您可以下載包含這些作業從[Microsoft 連線網站](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)所用的指令程式的模組。

## <a name="create-settings-at-the-directory-level"></a>建立目錄層級的設定

這些步驟建立設定目錄層級套用至目錄中的所有 Office 群組。

1. 如果您不知道要使用哪個 SettingTemplate，這個指令程式會傳回設定範本的清單︰

    `Get-MsolAllSettingTemplate`

    ![設定範本清單](./media/active-directory-accessmanagement-groups-settings-cmdlets/list-of-templates.png)

2. 若要新增的使用狀況指導方針的 URL，首先您需要取得的定義使用方法指導方針的 URL 值; SettingsTemplate 物件就是 Group.Unified 範本︰

    `$template = Get-MsolSettingTemplate –TemplateId 62375ab9-6b52-47ed-826b-58e47e0e304b`

3. 接下來，建立該範本為基礎的新設定物件︰

    `$setting = $template.CreateSettingsObject()`

4. 然後更新使用方法指導方針的值︰

    `$setting["UsageGuidelinesUrl"] = "<https://guideline.com>"`

5. 最後，套用設定︰

    `New-MsolSettings –SettingsObject $setting`

    ![新增的使用方式的指導方針 URL](./media/active-directory-accessmanagement-groups-settings-cmdlets/add-usage-guideline-url.png)

以下是 Group.Unified SettingsTemplate 中定義的設定。

 **設定**                          | **描述**                                                                                             
--------------------------------------|-----------------------------------------------
 <ul><li>ClassificationList<li>類型︰ 字串<li>預設: 「 」                  | 逗點分隔可套用至整合群組的有效分類值的清單。                
 <ul><li>EnableGroupCreation<li>類型︰ 布林值<li>預設:，則為 True              | 指出是否允許整合群組建立目錄中的旗標。                               
 <ul><li>GroupCreationAllowedGroupId<li>類型︰ 字串<li>預設: 「 」         | 允許建立整合群組的 [安全性] 群組的 GUID 即使 EnableGroupCreation = = false。
 <ul><li>UsageGuidelinesUrl<li>類型︰ 字串<li>預設: 「 」                  | 群組使用方針連結。                                                                       

## <a name="read-settings-at-the-directory-level"></a>讀取目錄層級設定

這些步驟閱讀目錄層級設定套用至目錄中的所有 Office 群組。

1. 讀取所有現有的目錄設定︰

    `Get-MsolAllSettings`

2. 讀取特定群組的所有設定︰

    `Get-MsolAllSettings -TargetType Groups -TargetObjectId <groupObjectId>`

3. 閱讀使用 SettingId GUID 的特定目錄設定︰

    `Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

    ![設定 ID GUID](./media/active-directory-accessmanagement-groups-settings-cmdlets/settings-id-guid.png)

## <a name="update-settings-at-the-directory-level"></a>更新目錄層級設定

這些步驟更新目錄層級設定套用至目錄中的所有 Office 群組。

1. 取得現有的設定物件︰

    `$setting = Get-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

2. 取得您想要更新的值︰

    `$value = $Setting.GetSettingsValue()`

3. 更新的值︰

    `$value["AllowToAddGuests"] = "false"`

4. 更新設定︰

    `Set-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c –SettingsValue $value`

## <a name="remove-settings-at-the-directory-level"></a>移除目錄層級設定

此步驟中移除目錄層級設定套用至目錄中的所有 Office 群組。

    `Remove-MsolSettings –SettingId dbbcb0ea-a6ff-4b44-a1f3-9d7cef74984c`

## <a name="cmdlet-syntax-reference"></a>Cmdlet 語法參照

您可以在[Azure Active Directory Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=808260)，以找到更多的 Azure Active Directory PowerShell 文件。

## <a name="settingstemplate-object-reference-groupunified-settingstemplate-object"></a>SettingsTemplate 物件參照 （Group.Unified SettingsTemplate 物件）

- 「 名稱 」: 「 EnableGroupCreation 」，「 type 」: 「 System.Boolean 」，「 預設 」: 「 true 」、 「 描述 」: 」，則為 true 表示旗標整合群組建立功能是否。 」

- 「 名稱 」: 「 GroupCreationAllowedGroupId 」，「 type 」: 「 System.Guid 」，「 預設 」: 「 」，「 描述 」: 」 是建立整合群組 whitelisted 安全性群組 GUID 」。

- 「 名稱 」: 「 ClassificationList 」，「 類型 」: 「 System.String 」，「 預設 」: 「 」，「 描述 」: 「 以逗號分隔的清單可套用至整合群組的有效分類值。 」

- 「 名稱 」: 「 UsageGuidelinesUrl 」，「 type 」: 「 System.String 」，「 預設 」: 「 」，「 描述 」: 「 群組使用方針的連結]。

名稱 | 類型 | 預設值] | 描述
----------  | ----------  | ---------  | ----------
「 EnableGroupCreation 」  | 「 System.Boolean 」  | "true"  | 「 布林旗標，表示整合群組建立功能是否。 」
「 GroupCreationAllowedGroupId 」  | 「 System.Guid 」  | ""  | 「 GUID whitelisted 建立整合群組的 [安全性] 群組的 「。
「 ClassificationList 」  | 「 System.String 」  | ""  | 「 逗點分隔有效分類值清單，可套用統一的群組。 」
「 UsageGuidelinesUrl 」  | 「 System.String 」  | ""  | 「 群組使用方針連結 」。

## <a name="next-steps"></a>後續步驟

您可以在[Azure Active Directory Cmdlet](http://go.microsoft.com/fwlink/p/?LinkId=808260)，以找到更多的 Azure Active Directory PowerShell 文件。

從 Microsoft 程式管理員葉 de Jong 其他指示的[葉的群組部落格](http://robsgroupsblog.com/blog/configuring-settings-for-office-365-groups-in-azure-ad)。

* [管理資源的存取權與 Azure Active Directory 群組](active-directory-manage-groups.md)

* [整合您的內部部署識別與 Azure Active Directory](active-directory-aadconnect.md)
