<properties
    pageTitle="Azure Active Directory 登入活動報表 API 參考 |Microsoft Azure"
    description="Azure Active Directory 登入活動報表 API 參考"
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
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Azure Active Directory 登入活動報表 API 參考


本主題是集合的報告 API Azure Active Directory 的相關主題。  
Azure AD 報告提供的 API 可讓您存取登入活動報表資料使用的程式碼或相關的工具。
本主題的範圍是為您提供有關**登入活動報表 API**參考資訊。

請參閱︰

- 如需詳細的概念性資訊[登入活動](active-directory-reporting-azure-portal.md#sign-in-activities)
- 如需有關報告 API， [Azure Active Directory 報告 API 快速入門](active-directory-reporting-api-getting-started.md)。

問題、 問題或意見反應，請連絡[AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。



## <a name="who-can-access-the-api-data"></a>誰可以存取的 API 資料？

- 安全性系統管理員或安全性的閱讀程式角色的使用者

- 全域管理員

- 已授權存取 API 任何應用程式 （應用程式授權可設定只會根據全域管理員權限）



## <a name="prerequisites"></a>必要條件

若要存取此報告到報告 API，您必須︰

- [Azure Active Directory 進階版 P1 或 P2 版本](active-directory-editions.md)

- 完成[存取報告 API Azure AD 的先決條件](active-directory-reporting-api-prerequisites.md)。 


##<a name="accessing-the-api"></a>存取 API

您可能可以存取此 API 透過[圖形總管](https://graphexplorer2.cloudapp.net)或以程式設計方式，例如，使用 PowerShell。 為了讓 PowerShell 以正確解譯 AAD 圖形其餘通話中使用 OData 篩選語法，您必須使用反 (又稱︰ 抑音符號) 「 逸出 「 $ 字元的字元。 反單引號字元作為[PowerShell 的使用逸出字元](https://technet.microsoft.com/library/hh847755.aspx)，允許 PowerShell 來執行 $ 字元常值解譯並避免混淆，與 PowerShell 變數名稱 (例如︰ $filter)。

本主題的焦點會放在 [圖形總管]。 如需 PowerShell 範例，請參閱此[PowerShell 指令碼](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script)。


## <a name="api-endpoint"></a>API 端點

您可以存取此 API 使用下列基底 URI:  
    
    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



資料量，因為此 API 有一百萬傳回記錄的限制。 

這個呼叫批次中傳回的資料。 每個批次中有 1000年筆記錄的最大值。  
若要取得下一個批次的記錄，請使用 [下一步] 連結。 從 [傳回記錄的第一組取得[skiptoken](https://msdn.microsoft.com/library/dd942121.aspx)的資訊。 略過權杖會結尾結果的設定。  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>支援的篩選

您可以縮小所傳回的 API 的記錄數目撥入篩選的表單。  
登入 API 的相關資料，支援下列篩選器︰

- **$top =\<記錄来傳回的數字\>**-限制傳回的記錄數目。 這是寶貴的作業。 如果您想要傳回數以千計的物件，您不應使用這個篩選器。  
- **$filter =\<您篩選陳述式\>**-支援的篩選的欄位，以指定的記錄您關注的資訊類型



## <a name="supported-filter-fields-and-operators"></a>支援的篩選欄位和運算子

若要指定的記錄您關注的資訊類型，您可以建立的篩選陳述式包含一個或下的 [篩選] 欄位的組合︰

- [signinDateTime](#signindatetime) -定義的日期或日期範圍

- [使用者識別碼](#userid)-定義特定的使用者會根據使用者的識別碼。

- [userPrincipalName](#userprincipalname) -定義特定的使用者會根據使用者的使用者主要名稱 (UPN)

- [appId](#appid) -定義特定應用程式基礎的應用程式識別碼

- [appDisplayName](#appdisplayname) -定義特定應用程式以應用程式的顯示名稱

- [loginStatus](#loginStatus) -定義的登入狀態 (成功 / 失敗)


> [AZURE.NOTE] 使用圖形總管]，您需要使用正確的大小寫字母時，您篩選的欄位。


若要縮小所傳回的資料範圍，您可以建立支援的篩選及篩選欄位的組合。 例如，下列陳述式會傳回介於年 7 月 1 2016 年 7 月 6 2016年前的 10 筆記錄︰

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


----------

### <a name="signindatetime"></a>signinDateTime

**支援運算子**︰ eq，ge，le gt、 lt

**範例**︰

使用特定的日期

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z  



使用 [日期範圍    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**附註**︰

Datetime 參數應該 UTC 格式 


----------

### <a name="userid"></a>使用者識別碼

**支援運算子**︰ eq

**範例**︰

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**附註**︰

使用者識別碼的值是字串值



----------

### <a name="userprincipalname"></a>userPrincipalName

**支援運算子**︰ eq

**範例**︰

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**附註**︰

UserPrincipalName 值是字串值

----------

### <a name="appid"></a>appId

**支援運算子**︰ eq

**範例**︰

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**附註**︰

AppId 的值是字串值

----------


### <a name="appdisplayname"></a>appDisplayName

**支援運算子**︰ eq

**範例**︰

    $filter=appDisplayName+eq+'Azure+Portal' 


**附註**︰

AppDisplayName 的值是字串值

----------

### <a name="loginstatus"></a>loginStatus

**支援運算子**︰ eq

**範例**︰

    $filter=loginStatus+eq+'1'  


**附註**︰

有兩種選項 loginStatus: 0-成功，1-失敗

----------



## <a name="next-steps"></a>後續步驟

- 您想要查看篩選的登入活動範例？ 請參閱[Azure Active Directory 登入活動報表 API 範例](active-directory-reporting-api-sign-in-activity-samples.md)。

- 您想要深入瞭解報告 API Azure AD？ 請參閱[快速入門 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。