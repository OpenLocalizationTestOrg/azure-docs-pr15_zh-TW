<properties
    pageTitle="若要存取報告 API Azure AD 的先決條件。 |Microsoft Azure"
    description="深入瞭解存取報告 API Azure AD 先決條件"
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

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>若要存取報告 API Azure AD 的先決條件 

[Azure AD 報告 Api](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)提供給您透過其餘的 Api 一組資料以程式設計方式存取。 您可以從各種不同的程式設計語言和工具呼叫這些 Api。

報表的 API 使用[OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx)授權網頁 Api 的存取。 

若要準備您對報告 API 的存取，您必須︰

1. Azure AD 租用戶中建立的應用程式 

2. 授與應用程式適當的權限來存取 Azure AD 資料

3. 從您的目錄收集設定的設定

問題、 問題或意見反應，請連絡[AAD 報告協助](mailto:aadreportinghelp@microsoft.com)。


## <a name="create-an-azure-ad-application"></a>建立 Azure AD 應用程式

若要設定您的目錄存取 Azure AD 報告 API，您必須 Azure 傳統入口網站的全域管理員角色的成員目錄 Azure AD 租用戶也是 Azure 訂閱系統管理員帳戶登入。

> [AZURE.IMPORTANT] 執行 [認證] 底下，以 「 系統 」 權限，就像這樣的應用程式可以很強大，因此請務必讓應用程式的識別碼/私人認證安全。


1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 

2. 從 [ **active directory** ] 清單中，選取 [目錄]。

3. 在頂端的功能表，按一下 [**應用程式**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png) 

4. 在下方的列中，按一下 [**新增**。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/03.png) 

5. 在**您想要做什麼？** ] 對話方塊中，按一下 [**新增組織內部開發的應用程式**。 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/04.png) 


6. 在 [**告訴我們瞭解您的應用程式**] 對話方塊中，執行下列步驟︰ 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/05.png) 

    。 在 [**名稱**] 文字方塊中，輸入的名稱 (例如︰ 報告 API 應用程式)。

    b。 選取**Web 應用程式及/或 web API**。

    c。 按一下 [**下一步**]。


7. 在 [**應用程式屬性**] 對話方塊中，執行下列步驟︰ 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/06.png) 

    。 在 [**登入 URL** ] 文字方塊中，輸入`https://localhost`。

    b。 在 [**應用程式識別碼 URI** ] 文字方塊中，輸入```https://localhost/ReportingApiApp```。

    c。 按一下 [**完成**]。



## <a name="grant-your-application-permission-to-use-the-api"></a>授與您使用的 API 的應用程式權限

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com/)，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 

2. 從 [ **active directory** ] 清單中，選取 [目錄]。

3. 在頂端的功能表，按一下 [**應用程式**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png)


3. 在 [應用程式] 清單中，選取您新建立的應用程式。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/07.png)

4. 在頂端的功能表，按一下 [**設定**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/08.png)


5. 在**其他應用程式的權限**] 區段中的**Azure Active Directory**資源，按一下 [**應用程式權限**] 下拉式清單中，然後按一下**讀取目錄資料**。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/09.png)


5. 在下方的列中，按一下 [**儲存**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>從您的目錄收集設定的設定

本節說明如何從您的目錄取得下列設定︰

- 網域名稱
- 用戶端識別碼
- 用戶端密碼

設定來電至] 報告 API 時，您會需要這些值。 


### <a name="get-your-domain-name"></a>取得您的網域名稱

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 

2. 從 [ **active directory** ] 清單中，選取 [目錄]。

3. 在頂端的功能表，按一下 [**網域**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/11.png) 

4. 在 [**網域名稱**] 欄中，複製您的網域名稱。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>取得應用程式的用戶端識別碼

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 

2. 從 [ **active directory** ] 清單中，選取 [目錄]。

3. 在頂端的功能表，按一下 [**應用程式**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png) 

4. 在 [應用程式] 清單中，選取您新建立的應用程式。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/07.png)

4. 在頂端的功能表，按一下 [**設定**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/08.png)

4. 複製您的**用戶端識別碼**。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>取得應用程式的用戶端密碼

若要取得應用程式的用戶端密碼，您需要建立新的機碼並儲存在儲存新的金鑰，因為不稍後再擷取這個值時，其值。

1. 在[Azure 傳統入口網站](https://manage.windowsazure.com)，在左側的功能窗格中，按一下 [ **Active Directory**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/01.png) 

2. 從 [ **active directory** ] 清單中，選取 [目錄]。

3. 在頂端的功能表，按一下 [**應用程式**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/02.png) 

4. 在 [應用程式] 清單中，選取您新建立的應用程式。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/07.png)

4. 在頂端的功能表，按一下 [**設定**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/08.png)

5. 在 [**索引鍵**] 區段中，執行下列步驟︰ 

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/14.png)

    。 從 [持續時間] 清單中，選取 [持續時間

    b。 在下方的列中，按一下 [**儲存**]。

    ![註冊應用程式](./media/active-directory-reporting-api-prerequisites/10.png)

    c。 將複製的值。

## <a name="next-steps"></a>後續步驟

- 您想要從 Azure AD 報告 API 以程式設計方式存取資料？ 請參閱[快速入門 Azure Active Directory 報告 API](active-directory-reporting-api-getting-started.md)。

- 如果您想要瞭解更多有關 Azure Active Directory 報告，請參閱[Azure Active Directory 報告指南](active-directory-reporting-guide.md)。  
