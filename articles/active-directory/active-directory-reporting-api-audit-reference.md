<properties
    pageTitle="Azure Active Directory 稽核 API 參考 |Microsoft Azure"
    description="如何開始使用 Azure Active Directory 稽核 API"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/24/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-audit-api-reference"></a>Azure Active Directory 稽核 API 參考

本主題是集合的報告 API Azure Active Directory 的相關主題。  
Azure AD 報告提供的 API 可讓您存取稽核資料使用的程式碼或相關的工具。
本主題的範圍是為您提供的**稽核 API**的參考資訊。

請參閱︰

- 如需詳細的概念性資訊的[稽核記錄](active-directory-reporting-azure-portal.md#audit-logs)
- 如需有關報告 API， [Azure Active Directory 報告 API 快速入門](active-directory-reporting-api-getting-started.md)。

問題、 問題或意見反應，請連絡[AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。


## <a name="who-can-access-the-data"></a>誰能夠存取資料？

- 安全性系統管理員或安全性助讀程式角色的使用者

- 全域管理員

- 已授權存取 API 任何應用程式 （應用程式授權可設定只會根據全域管理員權限）



## <a name="prerequisites"></a>必要條件

若要存取此報告到報告 API，您必須︰

- [Azure Active Directory 免費或更好的版本](active-directory-editions.md)

- 完成[存取報告 API Azure AD 的先決條件](active-directory-reporting-api-prerequisites.md)。 
 

##<a name="accessing-the-api"></a>存取 API

您可能可以存取此 API 透過[圖形總管](https://graphexplorer2.cloudapp.net)或以程式設計方式，例如，使用 PowerShell。 為了讓 PowerShell 以正確解譯 AAD 圖形其餘通話中使用 OData 篩選語法，您必須使用反 (又稱︰ 抑音符號) 「 逸出 「 $ 字元的字元。 反單引號字元作為[PowerShell 的使用逸出字元](https://technet.microsoft.com/library/hh847755.aspx)，允許 PowerShell 來執行 $ 字元常值解譯並避免混淆，與 PowerShell 變數名稱 (例如︰ $filter)。

本主題的焦點會放在 [圖形總管]。 如需 PowerShell 範例，請參閱此[PowerShell 指令碼](active-directory-reporting-api-audit-samples.md#powershell-script)。

 
 

## <a name="api-endpoint"></a>API 端點


您可以存取此 API 使用下列 URI:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

沒有任何限制 Azure AD 稽核 API （使用 OData 分頁） 所傳回的記錄數目。
報告資料保留限制，請參閱[報告保留原則](active-directory-reporting-retention.md)。

這個呼叫批次中傳回的資料。 每個批次中有 1000年筆記錄的最大值。  
若要取得下一個批次的記錄，請使用 [下一步] 連結。 從 [傳回記錄的第一組取得 skiptoken 的資訊。 略過權杖會結尾結果的設定。  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>支援的篩選

您可以縮小所傳回的 API 的記錄數目撥入篩選的表單。  
登入 API 的相關資料，支援下列篩選器︰

- **$top =\<記錄来傳回的數字\>**-限制傳回的記錄數目。 這是寶貴的作業。 如果您想要傳回數以千計的物件，您不應使用這個篩選器。     
- **$filter =\<您篩選陳述式\>**-支援的篩選的欄位，以指定的記錄您關注的資訊類型



## <a name="supported-filter-fields-and-operators"></a>支援的篩選欄位和運算子

若要指定的記錄您關注的資訊類型，您可以建立的篩選陳述式包含一個或下的 [篩選] 欄位的組合︰

- [activityDate](#activitydate) -定義的日期或日期範圍
- [activityType](#activitytype) -定義活動的類型
- [活動](#activity)-定義為字串的活動  
- [動作項目/名稱](#actorname)-演員名稱的表單中定義的動作項目
- [動作項目/objectid](#actorobjectid) -定義的動作項目在表單中的動作項目識別碼   
- [動作項目/upn](#actorupn) -定義的動作項目的使用者主要名稱 (UPN) 的表單中的 [動作項目 
- [目標/名稱](#targetname)-定義的動作項目名稱的表單中的目標
- [目標/objectid](#targetobjectid) -定義目標在表單中的 [目標的識別碼  
- [目標/upn](#targetupn) -定義的動作項目的使用者主要名稱 (UPN) 的表單中的 [動作項目   




----------

### <a name="activitydate"></a>activityDate

**支援運算子**︰ eq，ge，le gt、 lt

**範例**︰

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=eventTime gt ' + $7daysago    

**附註**︰

datetime 應該是 UTC 格式

----------

### <a name="activitytype"></a>activityType

**支援運算子**︰ eq

**範例**︰

    $filter=activityType eq 'User'  

**附註**︰

區分大小寫

----------

### <a name="activity"></a>活動

**支援運算子**︰ eq，包含，startsWith

**範例**︰

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')   

**附註**︰

區分大小寫

----------

### <a name="actorname"></a>動作項目/名稱

**支援運算子**︰ eq，包含，startsWith

**範例**︰

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')  

**附註**︰

不區分大小寫

    

----------
### <a name="actorobjectid"></a>動作項目/objectId

**支援運算子**︰ eq

**範例**︰

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    

----------
### <a name="targetname"></a>目標/名稱

**支援運算子**︰ eq，包含，startsWith

**範例**︰

    $filter=targets/any(t: t/name eq 'some name')   

**附註**︰

不區分大小寫

----------

### <a name="targetupn"></a>目標/upn

**支援運算子**︰ eq，startsWith

**範例**︰

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc')) 

**附註**︰

- 不區分大小寫
- 您需要查詢 Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity 時新增的完整命名空間

----------

### <a name="targetobjectid"></a>目標/objectId

**支援運算子**︰ eq

**範例**︰

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

----------

### <a name="actorupn"></a>動作項目/upn

**支援運算子**︰ eq，startsWith

**範例**︰

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')  

**附註**︰

- 不區分大小寫 
- 您需要查詢 Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity 時新增的完整命名空間

----------




## <a name="next-steps"></a>後續步驟

- 您想要查看篩選的系統活動範例？ 請參閱[Azure Active Directory 稽核 API 範例](active-directory-reporting-api-audit-samples.md)。

- 您想要深入瞭解報告 API Azure AD？ 請參閱[快速入門 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。