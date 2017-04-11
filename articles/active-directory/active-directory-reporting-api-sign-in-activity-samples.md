<properties
    pageTitle="Azure Active Directory 登入活動報表 API 範例 |Microsoft Azure"
    description="如何開始使用 Azure Active Directory 報告 API"
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

# <a name="azure-active-directory-sign-in-activity-report-api-samples"></a>Azure Active Directory 登入活動報表 API 範例

本主題是集合的報告 API Azure Active Directory 的相關主題。  
Azure AD 報告提供的 API 可讓您存取登入活動資料使用的程式碼或相關的工具。  
本主題的範圍是可讓您的**登入活動 API**範例程式碼。

請參閱︰

- 如需詳細的概念性資訊的[稽核記錄](active-directory-reporting-azure-portal.md#audit-logs)
- 如需有關報告 API， [Azure Active Directory 報告 API 快速入門](active-directory-reporting-api-getting-started.md)。

問題、 問題或意見反應，請連絡[AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。


## <a name="prerequisites"></a>必要條件
您可以在本主題中使用範例之前，必須先完成[存取報告 API Azure AD 的先決條件](active-directory-reporting-api-prerequisites.md)。  


##<a name="powershell-script"></a>PowerShell 指令碼

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID       = "<clientId>"             # Should be a ~35 character string insert your info here
    $ClientSecret   = "<clientSecret>"         # Should be a ~44 character string insert your info here
    $loginURL       = "https://login.windows.net/"
    $tenantdomain   = "<tenantDomain>"
    $ daterange            # For example, contoso.onmicrosoft.com

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}

    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    $url = "https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&`$filter=signinDateTime ge $7daysago"
    
    $i=0
    
    Do{
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        Write-Output "Save the output to a file SigninActivities$i.json"
        Write-Output "---------------------------------------------"
        $myReport.Content | Out-File -FilePath SigninActivities$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)

    } else {
    
        Write-Host "ERROR: No Access Token"
    }




## <a name="executing-the-script"></a>執行指令碼
當您完成編輯指令碼、 執行及驗證預期的資料的稽核記錄報告會傳回。

指令碼傳回輸出中的登入報告 JSON 格式。 也會建立`SigninActivities.json`相同的輸出檔案。 您可以嘗試修改資料傳回從其他報表和註解您不需要的輸出格式的指令碼。



## <a name="next-steps"></a>後續步驟

- 您想要自訂本主題中的範例？ 請參閱[Azure Active Directory 登入活動 API 參考](active-directory-reporting-api-sign-in-activity-reference.md)。 

- 如果您要查看完整的使用報告 API Azure Active Directory 概觀，請參閱[快速入門 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。

- 如果您想要瞭解更多有關 Azure Active Directory 報告，請參閱[Azure Active Directory 報告指南](active-directory-reporting-guide.md)。  