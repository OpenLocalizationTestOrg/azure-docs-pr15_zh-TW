<properties
    pageTitle="開始使用 Azure Active Directory 身分識別保護與 Microsoft Graph |Microsoft Azure"
    description="從 Azure Active Directory 風險事件和相關的資訊清單提供 Microsoft Graph 查詢簡介。"
    services="active-directory"
    keywords="azure active directory 身分識別保護、 風險事件、 弱點、 安全性原則，Microsoft Graph"
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Azure Active Directory 身分識別保護與 Microsoft Graph 快速入門

Microsoft Graph 是 Microsoft 的整合的 API 端點與[Azure Active Directory 身分識別保護的](active-directory-identityprotection.md)Api 的首頁。 我們第一個 API **identityRiskEvents**，可讓您查詢 Microsoft Graph[風險事件](active-directory-identityprotection-risk-events-types.md)和相關的資訊的清單。 本文可取得您開始查詢此 API。 深入的簡介與圖形總管] 中，存取完整的文件，請參閱[Microsoft Graph 網站](https://graph.microsoft.io/)。

有三個步驟即可透過 Microsoft Graph 存取身分識別保護資料︰

1. 新增具有用戶端私人的應用程式。 

2. 使用此密碼] 和 [資訊的幾個其他項目向 Microsoft graph 下方，您會收到的驗證的權杖進行驗證。 

3. 使用此要求 API 結束點，然後返回身分識別保護資料。

在您開始之前，您必須︰

- Azure AD 中建立的應用程式的系統管理員權限
- 您的租用戶網域 (例如 contoso.onmicrosoft.com) 的名稱



## <a name="add-an-application-with-a-client-secret"></a>新增與用戶端私人應用程式


1. [登入](https://manage.windowsazure.com)您的 Azure 傳統入口網站，以系統管理員。 

1. 在左側的功能窗格中，按一下 [ **Active Directory**。 

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. 從 [**目錄**] 清單中，選取您要啟用目錄整合的目錄。

3. 在頂端的功能表，按一下 [**應用程式**]。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. 按一下 [**新增**頁面的底部。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. 在 [**您想要做什麼**] 對話方塊中，按一下 [**新增組織內部開發的應用程式**。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. 在 [**告訴我們瞭解您的應用程式**] 對話方塊中，執行下列步驟︰

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    。 在 [**名稱**] 文字方塊中，輸入您的應用程式的名稱 (例如︰ AADIP 風險事件 API 應用程式)。

    b。 [**類型**] 中，選取 [ **Web 應用程式或網頁的 API**。

    c。 按一下 [**下一步**]。


5. 在 [**應用程式屬性**] 對話方塊中，執行下列步驟︰

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    。 在 [**登入 URL** ] 文字方塊中，輸入`http://localhost`。

    b。 在 [**應用程式識別碼 URI** ] 文字方塊中，輸入`http://localhost`。

    c。 按一下 [**完成**]。


您可以設定您的應用程式]。

![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>授與您使用的 API 的應用程式權限


1. 在您的應用程式] 頁面的功能表頂端，按一下 [**設定**]。 

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. 在**其他應用程式的權限**] 區段中，按一下 [**新增應用程式**。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. 在 [**其他應用程式的權限**] 對話方塊中，執行下列步驟︰

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    。 選取 [ **Microsoft Graph**]。

    b。 按一下 [**完成**]。


1. 按一下 [**應用程式權限︰ 0**，然後選取 [**讀取所有的身分識別風險事件資訊**。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. 按一下 [在頁面底部的 [**儲存**]。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>取得便捷鍵

1. 在您的應用程式頁面上，在**索引鍵**] 區段中，選取 1 年為期間。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. 按一下 [在頁面底部的 [**儲存**]。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. 在 [索引鍵] 區段中，複製您新建立的索引鍵值，並再貼到 [安全的位置。

    ![建立應用程式](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] 如果您遺失了此鍵，您必須返回這一節，然後建立新的金鑰。 保留此按鍵密碼︰ 任何人只要有它可以存取您的資料。


1. 在 [**屬性**] 區段中，複製**的用戶端識別碼**，，然後再貼到 [安全的位置。 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>Microsoft graph 驗證及查詢的身分識別風險事件 API

此時，您應該有︰

- 您剛才複製的用戶端識別碼

- 您複製上方的金鑰

- 您的租用戶網域的名稱


若要進行驗證，傳送的文章要求`https://login.microsoft.com`本文中的下列參數︰

- grant_type: 「**client_credentials**」

- 資源: 「**https://graph.microsoft.com**」

- client_id:<your client ID>

- client_secret:<your key>


> [AZURE.NOTE] 您需要提供**client_id**和**client_secret**參數的值。

如果成功，這樣就會傳回驗證權杖。  
若要打電話 API，建立的標頭下列參數︰

    `Authorization`=”<token_type> <access_token>"


驗證時，您可以在傳回的權杖中找到的權杖類型和存取權杖。

傳送︰ 此標題以要求下列 API URL:`https://graph.microsoft.com/beta/identityRiskEvents`

回應，如果成功，是身分識別風險事件和 OData JSON 格式，可以剖析和處理它們的相關的資料集合。

以下是範例驗證與呼叫使用 Powershell API 的程式碼。  
只要在新增您的用戶端識別碼按鍵，並租用戶的網域。

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>後續步驟

恭喜您，您剛才所做的 Microsoft Graph 您第一次呼叫 ！  
現在您可以查詢身分識別風險事件，並使用資料，不過請參閱調整。

若要進一步瞭解 Microsoft Graph，以及如何使用圖形 API 建置應用程式，檢查取出[文件](https://graph.microsoft.io/docs)及更多[Microsoft Graph 網站](https://graph.microsoft.io/)上。 此外，請確定書籤[Azure AD 身分識別保護 API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)頁面會列出所有的身分識別保護 Api 可用的圖表。 我們加入新的方法與身分識別保護透過 API 搭配使用，您會看到該頁面上。


## <a name="additional-resources"></a>其他資源

- [Azure Active Directory 身分識別保護](active-directory-identityprotection.md)

- [偵測到的 Azure Active Directory 身分識別保護風險事件的類型](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Microsoft Graph 的概觀](https://graph.microsoft.io/docs)

- [Azure AD 身分識別保護服務根](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)
