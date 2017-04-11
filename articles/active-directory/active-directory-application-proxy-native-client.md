<properties
    pageTitle="如何啟用發佈的原生的用戶端應用程式與 proxy 應用程式 |Microsoft Azure"
    description="說明如何啟用 Azure AD 應用程式 Proxy 連接器，以提供安全遠端存取您內部部署的應用程式與通訊的原生的用戶端應用程式。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>如何啟用互動 proxy 應用程式的原生的用戶端應用程式

Azure Active Directory 應用程式 Proxy 廣泛用於發佈瀏覽器應用程式，例如 SharePoint、 Outlook Web Access 和商務應用程式的自訂線條。 也可以發佈原生的用戶端應用程式，因為在裝置上安裝不同 web 應用程式的使用。 這是支援 Azure AD 發出的權杖傳送標準的授權 HTTP 標題中。

![使用者、 Azure Active Directory 和發佈的應用程式之間的關聯](./media/active-directory-application-proxy-native-client/richclientflow.png)

發佈這類應用程式的建議的方法是使用 Azure AD 驗證文件庫，其會處理的所有驗證容易並支援許多不同的用戶端的環境。 應用程式 Proxy 適合[Web API 案例的原生應用程式](active-directory-authentication-scenarios.md#native-application-to-web-api)。 可以完成此程序如下所示︰

## <a name="step-1-publish-your-application"></a>步驟 1︰ 發佈您的應用程式

發佈您的 proxy 應用程式，就像您任何其他應用程式，為使用者指派並賦予其 premium 或基本的授權。 如需詳細資訊，請參閱[使用應用程式 Proxy 發佈應用程式](active-directory-application-proxy-publish.md)。

## <a name="step-2-configure-your-application"></a>步驟 2︰ 設定您的應用程式

設定您的原生應用程式如下所示︰

1. Azure 傳統入口網站登入。
2. 選取左側] 功能表上的 [Active Directory] 圖示，然後選取 [您的目錄。
3. 在頂端的功能表中，按一下 [**應用程式**。 如果沒有應用程式已新增至您的目錄，此頁面只會顯示 [**新增應用程式**] 連結。 按一下連結，或或者您可以按一下命令列上的 [**新增**] 按鈕上。
4. **您想要**在頁面上，按一下連結至 [**新增組織內部開發的應用程式**。
5. 在 [**告訴我們瞭解您的應用程式**] 頁面中，指定您的應用程式的名稱，然後選擇 [**原生的用戶端應用程式**]。 按一下箭號圖示，以繼續進行。
6. 在 [**應用程式的資訊**] 頁面上提供**重新導向 URI**原生的用戶端應用程式，然後按一下 [完成核取記號。

將新增您的應用程式，您會被重新導向至 [快速入門] 頁面的應用程式。

## <a name="step-3-grant-access-to-other-applications"></a>步驟 3︰ 授與存取權，其他應用程式

啟用公開目錄中的其他應用程式的原生應用程式︰

1. 在頂端的功能表中，按一下 [**應用程式**，選取新的原生應用程式，，然後按一下 [**設定**。
2. 向**其他應用程式的權限**] 區段下捲動。 按一下 [**新增應用程式**] 按鈕，選取您要授與原生應用程式存取的 proxy 應用程式並按一下右下角的核取記號。 從 [**委派的權限**下拉式] 功能表中，選取 [新的權限。

![其他應用程式的螢幕擷取畫面的權限新增應用程式](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>步驟 4︰ 編輯 Active Directory 驗證文件庫

編輯原生應用程式中的程式碼驗證內容的 Active Directory 驗證文件庫 (ADAL) 若要包括下列各項︰

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

變數應該取代，如下所示︰

- **TenantId**中找應用程式的**設定**] 頁面的 URL 中 GUID 之後 」 / 目錄 / 」。
- **Frontend 的 URL**是您在 Proxy 應用程式中輸入，並可以找到 proxy 應用程式的 [**設定**] 頁面上的前端 URL。
- **用戶端識別碼**的原生應用程式可以找到的原生應用程式的 [**設定**] 頁面上。
- **重新導向 URI 原生應用程式**可以找到的原生應用程式的 [**設定**] 頁面上。

![新的原生應用程式設定] 頁面的螢幕擷取畫面](./media/active-directory-application-proxy-native-client/new_native_app.png)

如需有關原生應用程式的流程的詳細資訊，請參閱[網路 API 原生應用程式](active-directory-authentication-scenarios.md#native-application-to-web-api)。


## <a name="see-also"></a>另請參閱

- [發佈應用程式使用您自己的網域名稱](active-directory-application-proxy-custom-domains.md)
- [啟用條件的存取](active-directory-application-proxy-conditional-access.md)
- [使用 [宣告應用程式](active-directory-application-proxy-claims-aware-apps.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
