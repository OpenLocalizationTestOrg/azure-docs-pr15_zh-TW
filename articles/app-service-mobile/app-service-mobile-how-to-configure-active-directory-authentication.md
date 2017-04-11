<properties
    pageTitle="如何設定應用程式服務應用程式的 Azure Active Directory 驗證"
    description="瞭解如何設定您的應用程式服務應用程式的 Azure Active Directory 驗證。"
    authors="mattchenderson"
    services="app-service"
    documentationCenter=""
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="mahender"/>

# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>如何設定您的應用程式服務應用程式使用 Azure Active Directory 登入

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

本主題說明如何設定驗證提供者為使用 Azure Active Directory Azure 應用程式服務。

## <a name="express"></a>設定 Azure Active Directory 使用快速設定

13. 在[Azure 入口網站]，瀏覽至您的應用程式。 按一下 [**設定**]，再按一下 [**驗證和授權**。

14. 如果驗證 / 未啟用授權功能，請開啟切換到**上**。

15. 按一下**Azure Active Directory**]，然後按一下 [在**管理模式**] 底下的 [**快速**。

16. 按一下 [註冊 Azure Active Directory 中的應用程式的**[確定]** 。 這會建立新的註冊。 如果您想要改為選擇現有的註冊，請按一下 [**選取現有的應用程式**，然後搜尋先前建立的註冊您的租用戶中的名稱。
按一下 [註冊加以選取，然後按一下**[確定**]。 Azure Active Directory 設定刀上，然後按一下**[確定]** 。

    ![][0]

    根據預設，應用程式服務會提供驗證，但不會限制您的網站內容和 Api 未經授權的存取。 在您的應用程式碼中，您就必須授權的使用者。

17. （選用）若要限制存取至您的網站，由 Azure Active Directory 驗證的使用者，請登**入 Azure Active Directory**設定**要求未經驗證時採取的動作**。 這需要驗證所有要求，並在所有未經驗證的要求重新導向至 Azure Active Directory 驗證。

17. 按一下 [**儲存**]。

您已準備好要用於驗證您的應用程式中的 Azure Active Directory。

## <a name="advanced"> </a>（另一種方法） 以手動方式設定進階設定 Azure Active Directory
您也可以選擇以提供設定以手動方式。 如果您想要使用的 AAD 租用戶的租用戶您登入 Azure 不同，這是較佳的解決方案。 若要完成設定，您必須先建立註冊 Azure Active Directory 中，就必須先提供一些註冊詳細資料至應用程式服務。

### <a name="register"></a>與 Azure Active Directory 中註冊您的應用程式

1. 登入至[Azure 入口網站]，並瀏覽至您的應用程式。 複製您的**URL**。 您將使用此設定您的 Azure Active Directory 應用程式。

3. 登入[Azure 傳統入口網站]，然後瀏覽到**Active Directory**。

    ![][2]

4. 選取您的目錄，然後按一下頂端的 [**應用程式**] 索引標籤。 若要建立新的應用程式註冊底部，按一下 [**新增**]。

5. 按一下 [**新增組織內部開發的應用程式**。

6. 在 [新增應用程式精靈] 輸入您的應用程式的**名稱**，然後按一下**Web 應用程式和/或 Web API**類型。 然後按一下 [繼續。

7. 在 [**登開啟 URL** ] 方塊中，貼上您先前複製的應用程式 URL。 在 [**應用程式識別碼 URI** ] 方塊中輸入該相同的 URL。 然後按一下 [繼續。

8. 一旦加入應用程式，請按一下 [**設定**] 索引標籤。 編輯**回覆 URL**下**單一登入**，加上的路徑， _/.auth/login/aad/callback_應用程式的 URL。 例如， `https://contoso.azurewebsites.net/.auth/login/aad/callback`。 請確認您使用的 HTTPS 配置。

    ![][3]

9. 按一下 [**儲存**]。 然後複製應用程式的**用戶端識別碼**。 您將設定以供日後使用此應用程式。

10. 底部命令列中，按一下**檢視結束點**，然後複製**同盟中繼資料文件**的 URL 及下載文件，或在瀏覽器中瀏覽至該。

11. 在根**EntityDescriptor**元素，應該**entityID**屬性表單的`https://sts.windows.net/`後面特定 GUID 至您的租用戶 （稱為 「 租用戶識別碼 」）。 複製這個值-會成為您的**發行者的 URL**。 您將設定以供日後使用此應用程式。

### <a name="secrets"></a>應用程式新增 Azure Active Directory 資訊

13. 回[Azure 入口網站]中，瀏覽至您的應用程式。 按一下 [**設定**]，再按一下 [**驗證和授權**。

14. 如果未啟用驗證和授權功能，開啟切換到**上**。

15. 按一下**Azure Active Directory**]，然後按一下 [在**管理模式**] 底下的 [**進階]** 。 貼上您在先前取得的用戶端識別碼和簽發者 URL 值。 然後按一下**[確定]**。

    ![][1]

    根據預設，應用程式服務會提供驗證，但不會限制您的網站內容和 Api 未經授權的存取。 在您的應用程式碼中，您就必須授權的使用者。

17. （選用）若要限制存取至您的網站，由 Azure Active Directory 驗證的使用者，請登**入 Azure Active Directory**設定**要求未經驗證時採取的動作**。 此需要驗證所有要求，並在所有未經驗證的要求重新導向至 Azure Active Directory 驗證。

17. 按一下 [**儲存**]。

您已準備好要用於驗證您的應用程式中的 Azure Active Directory。

## <a name="optional-configure-a-native-client-application"></a>（選用）設定原生的用戶端應用程式

Azure Active Directory 也可讓您註冊原生的用戶端，提供更多的控制對應的權限。 您需要這如果您想要執行使用的文件庫，例如**Active Directory 驗證庫**登入。

1. 瀏覽至[Azure 傳統入口網站]中的**Active Directory** 。

2. 選取您的目錄，然後按一下頂端的 [**應用程式**] 索引標籤。 若要建立新的應用程式註冊底部，按一下 [**新增**]。

3. 按一下 [**新增組織內部開發的應用程式**。

4. 在新增的應用程式精靈] 中，輸入**名稱**應用程式的然後按一下**原生的用戶端應用程式**類型。 然後按一下 [繼續。

5. 在 [**重新導向 URI** ] 方塊中，輸入您的網站_/.auth/login/done_端點，請使用 HTTPS 配置。 此值應該類似_https://contoso.azurewebsites.net/.auth/login/done_。 如果建立 Windows 應用程式，請改為使用[套件 SID](app-service-mobile-dotnet-how-to-use-client-library.md#package-sid)為 URI。

6. 一旦加入原生應用程式，請按一下 [**設定**] 索引標籤。 尋找的**用戶端識別碼**，並記下這個值。

7. 捲動**到其他應用程式的權限**] 區段下的頁面，然後按一下 [**新增應用程式**。

8. 搜尋註冊，讓您稍早的 web 應用程式，並按一下加號圖示。 然後按一下檢查以關閉對話方塊。 如果 web 應用程式無法找到、 瀏覽至其註冊以及新增新的回覆 URL （例如，您目前的 URL 的 HTTP 版本），按一下 [儲存]，然後重複這些步驟-應用程式應該顯示在清單中。

9. 在您剛才新增的新項目，開啟 [**委派的權限**] 下拉式清單，然後選取**Access 應用程式 （名稱）**。 然後按一下 [**儲存**]。

您現在已設定的原生的用戶端應用程式可以存取您的應用程式服務應用程式。

## <a name="related-content"></a>相關內容

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Azure 入口網站]: https://portal.azure.com/
[Azure 傳統入口網站]: https://manage.windowsazure.com/
[alternative method]:#advanced
