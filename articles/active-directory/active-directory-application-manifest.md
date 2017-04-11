<properties
   pageTitle="了解 Azure Active Directory 應用程式顯示 |Microsoft Azure"
   description="Azure Active Directory 應用程式資訊清單，代表 Azure AD 租用戶中的應用程式的身分識別設定，並用於加速 OAuth 授權、 同意體驗，及更多詳細的範圍。"
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>了解 Azure Active Directory 應用程式資訊清單

Azure AD 租用戶，提供常設身分識別設定應用程式，就必須向整合與 Azure Active Directory (AD) 的應用程式。 在執行階段，啟用允許外包和仲介處理驗證和授權透過 Azure AD 應用程式的案例參考此設定。 如需有關 Azure AD 應用程式模型的詳細資訊，請參閱[新增、 更新，及移除應用程式][ADD-UPD-RMV-APP]文章。

## <a name="updating-an-applications-identity-configuration"></a>更新應用程式的身分識別設定

有可用的更新應用程式的身分識別設定，在功能和度的問題，包括下列屬性的實際的多個選項︰

- ** [Azure 傳統入口網站的][AZURE-CLASSIC-PORTAL]網頁使用者介面**可讓您更新最常見的應用程式屬性。 這是最快速且最錯誤容易方式更新您的應用程式屬性，但不提供完整存取所有的屬性，例如接下來的兩種方法。
- 更多進階案例中必須更新不會顯示在 Azure 傳統入口網站的內容，您可以修改的**應用程式資訊清單**。 這是本文的焦點，並在下一節中啟動的更多詳細資料中所提及。
- 也可以**撰寫的應用程式使用[圖形 API] [GRAPH-API]**更新您的應用程式會要求最投入。 這可能是美觀的選項，但如果您正在撰寫的管理軟體，或需要更新應用程式屬性定期以自動的方式。

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>使用應用程式顯示更新應用程式的身分識別設定
透過[Azure 傳統入口網站][AZURE-CLASSIC-PORTAL]，您可以下載來管理您的應用程式的身分識別設定上, 傳 JSON 檔案表示，這稱為 「 應用程式資訊。 沒有實際的檔案儲存在目錄中。 應用程式顯示只的 HTTP GET 作業 Azure AD 圖形 API 應用程式的實體，而上傳是應用程式實體上的 HTTP 修補作業。

如此一來，若要深入瞭解格式與應用程式顯示的內容，您需要參考圖形 API[應用程式實體][APPLICATION-ENTITY]文件。 更新應用程式資訊清單上傳雖然可以執行的範例包括︰

- 您的網路 API 公開**Declare 權限範圍 (oauth2Permissions)** 。 請參閱[整合應用程式，與 Azure Active Directory]中的 「 公開網頁 Api 以其他應用程式 」 主題[INTEGRATING-APPLICATIONS-AAD]實作使用者模擬的詳細資訊的使用 oauth2Permissions 委派的權限的範圍。 如先前所述，記載應用程式的實體屬性的圖形 API[實體和複雜型別][APPLICATION-ENTITY]參考文章，包括 oauth2Permissions 屬性是類型[OAuth2Permission]的集合[APPLICATION-ENTITY-OAUTH2-PERMISSION]。
- **您的應用程式公開 Declare 應用程式角色 (appRoles)**。 應用程式的實體 appRoles 屬性是類型[AppRole]集合[APPLICATION-ENTITY-APP-ROLE]。 請參閱[角色存取控制在雲端應用程式中使用 Azure AD] [RBAC-CLOUD-APPS-AZUREAD]實作範例的文章。
- **Declare 已知的用戶端應用程式 (knownClientApplications)**，可讓您以邏輯方式會與指定的用戶端應用程式資源/網路 API 的同意相連。
- **要求發出群組成員資格宣告 Azure AD**登入的使用者 (groupMembershipClaims)。  這也要瞭解使用者的目錄角色的成員資格的宣告設定。 請參閱[授權雲端應用程式中的使用 AD 群組][AAD-GROUPS-FOR-AUTHORIZATION]文章的實作範例。
- **允許您的應用程式支援 OAuth 2.0 隱含授與**流 (oauth2AllowImplicitFlow)。 此類型的 [授與流程使用內嵌的 JavaScript 網頁或單一頁面的應用程式] 選項。 如需有關隱含授權授與的詳細資訊，請參閱[瞭解隱含 OAuth2 授與 Azure Active Directory 中的流程][IMPLICIT-GRANT]。
- **啟用使用 X509 憑證為私人的索引鍵**(keyCredentials)。 請參閱[建立 Office 365 中的服務與精靈相關應用程式]的[O365-SERVICE-DAEMON-APPS]和[Azure 資源管理員 api 驗證開發人員指南][DEV-GUIDE-TO-AUTH-WITH-ARM]文章，取得實作範例。
- **新增新的應用程式識別碼 URI**應用程式 (identifierURIs[])。 應用程式識別碼 Uri 用來識別應用程式在其 Azure AD 租用戶 （或跨多個 Azure AD 租用戶，針對多租用戶案例限定透過驗證的自訂網域時）。 要求的資源應用程式，或取得存取權杖資源應用程式的權限時，會使用這些項目。 當您更新此項目時，則相同都會更新至對應的服務主要 servicePrincipalNames [集合，其放在應用程式的主租用戶。

應用程式顯示也會提供追蹤您的應用程式註冊的狀態的好方法。 因為它是使用 JSON 格式，可以檢查檔案表示到您的來源控制項，以及您的應用程式的程式碼。

## <a name="step-by-step-example"></a>步驟步驟範例
現在讓逐步瞭解更新您的應用程式到應用程式顯示的身分識別設定所需的步驟。 我們將會醒目提示之前的範例，其中顯示如何將新的權限範圍宣告資源應用程式︰

1. 瀏覽至[Azure 傳統入口網站][AZURE-CLASSIC-PORTAL]並使用具有服務系統管理員或共同管理員權限的帳戶登入。

2. 您已驗證，向下捲動並選取 Azure 「 Active Directory 」 副檔名在左側的導覽面板 (1)，然後按一下之後 Azure AD 租用戶，您的應用程式在哪裡註冊 (2)。

    ![選取 Azure AD 租用戶][SELECT-AZURE-AD-TENANT]

3. 當 [目錄] 頁面，按一下 [」 應用程式 」 (1)] 頁面上方的以查看註冊租用戶中的應用程式清單。 然後找到您要更新清單中，按一下 [(2) 上的應用程式。

    ![選取 Azure AD 租用戶][SELECT-AZURE-AD-APP]

4. 現在，您已經選取 [應用程式的主頁面，請注意底部的頁面 (1) 」 管理資訊清單 」 功能。 如果您按一下這個連結，系統會提示您下載或上傳 JSON 資訊清單檔案。 按一下 「 下載資訊清單 」 (2)。 這將會立即依循下載確認對話方塊，提示您確認即可 「 下載資訊清單 」 (3)，然後在 [開啟] 或 [儲存檔案本機 (4)。

    ![管理資訊清單，請下載選項][MANAGE-MANIFEST-DOWNLOAD]

    ![下載資訊清單][DOWNLOAD-MANIFEST]

5. 在此範例中，我們儲存該檔案，讓我們在編輯器中開啟，變更 JSON，再儲存一次。 以下是 JSON 結構 Visual Studio JSON 編輯器內的外觀。 請注意，它所遵循的結構描述的[應用程式實體][APPLICATION-ENTITY]如我們先前所述︰

    ![更新資訊清單 JSON][UPDATE-MANIFEST]

    例如，假設我們想要實作公開我們資源應用程式 (API) 稱為 「 Employees.Read.All 「 新的權限，您可以直接新增新/第二個項目 oauth2Permissions 集合中，ie:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    必須是唯一的項目，因此您必須產生新全域唯一識別碼 (GUID) 的`"id"`屬性。 在此情況下，因為我們指定`"type": "User"`，此權限可以同意若要以任何帳戶都經過 Azure AD 租用戶中已註冊的資源/API 應用程式。 這會授與用戶端存取客戶代表應用程式權限。 描述與顯示名稱的字串用於期間同意和 Azure 傳統入口網站中的顯示。

6. 當您完成更新資訊清單時，請返回 Azure 傳統入口網站中的 Azure AD 應用程式頁面，然後按一下 「 管理資訊清單 」 功能一次 (1)。 但這次請選取 [上傳資訊清單 」] 選項 (2)。 類似於下載，您會被氣急敗壞再次使用第二個] 對話方塊中，提示您輸入 JSON 檔案的位置。 按一下 [瀏覽至檔案...」(3)，然後選取 JSON 檔案 (4)，請使用選擇檔案來上傳] 對話方塊，然後按 [開啟]。 一旦對話方塊消失，選取 [確定] 核取記號 (5)，然後將上傳您的資訊清單。  

    ![管理資訊清單上, 傳] 選項][MANAGE-MANIFEST-UPLOAD]

    ![上傳資訊清單 JSON][UPLOAD-MANIFEST]

    ![上傳資訊清單 JSON-確認][UPLOAD-MANIFEST-CONFIRM]

現在，儲存資訊清單時，您可以授與註冊我們新增新的權限的用戶端應用程式存取。 這次您可以使用 Azure 傳統入口網站的網站使用者介面，而非編輯用戶端應用程式的資訊清單︰  

1. 請先移至您要加入至新的 API 的存取權限的用戶端應用程式的 「 設定 」 頁面，然後按一下 [新增應用程式] 按鈕。
2. 然後您會顯示已註冊的資源應用程式 (Api) 租用戶中的清單。 按一下加號 / + 資源應用程式的名稱來選取它旁邊的符號。  
3. 然後按一下右下角的核取記號。
4. 當您返回您的用戶端設定] 頁面的 [新增應用程式] 區段中時，您會看到新的資源應用程式清單中。 如果您將游標停留在該資料列右側的 [委派的權限] 區段，您會看到顯示下拉式清單。 按一下清單，然後選取 [新的權限，才能將其新增至客戶的權限要求的清單。 注意︰ 此新的權限會儲存在 「 requiredResourceAccess 」 集合屬性中的用戶端應用程式的身分識別設定。

![其他應用程式的權限][PERMS-TO-OTHER-APPS]

![其他應用程式的權限][PERMS-SELECT-APP]

![其他應用程式的權限][PERMS-SELECT-PERMS]

就是它。 現在您的應用程式會使用其新的身分識別設定來執行。

## <a name="next-steps"></a>後續步驟
- 如需應用程式的應用程式和服務主要個物件之間的關聯性的詳細資訊，請參閱[應用程式與服務本金物件 Azure AD][AAD-APP-OBJECTS]。
- 請參閱[Azure AD 開發人員詞彙][AAD-DEVELOPER-GLOSSARY]定義的一些核心 Azure Active Directory (AD) 開發人員概念。

請若要提供意見反應，並協助我們精簡和圖案內容，使用 DISQUS 註解] 區段下方。

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

