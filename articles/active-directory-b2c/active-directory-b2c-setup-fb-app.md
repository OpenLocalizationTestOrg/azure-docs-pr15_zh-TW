<properties
    pageTitle="Azure Active Directory B2C: Facebook 設定 |Microsoft Azure"
    description="提供使用者皆受到 Azure Active Directory B2C 的應用程式中的 Facebook 帳戶註冊和登入。"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C︰ 消費者與 Facebook 帳戶提供註冊和登入

## <a name="create-a-facebook-application"></a>建立 Facebook 應用程式

若要使用的身分識別提供者中 Azure Active Directory (Azure AD) B2C Facebook，您需要建立的 Facebook 應用程式，並提供正確的參數。 您需要執行此動作的 Facebook 帳戶。 如果您沒有帳戶，您可以在[https://www.facebook.com/](https://www.facebook.com/)取得其。

1. 移至 [[適用於開發人員 Facebook](https://developers.facebook.com/)網站，並使用您的 Facebook 帳戶認證登入。
2. 如果您有不這麼做，您需要註冊為 Facebook 開發人員。 若要這麼做，請按一下 [**註冊**（在頁面的右上角），接受 Facebook 的原則，完成註冊步驟。
3. 按一下 [**我的應用程式**，然後按一下 [**新增新的應用程式**。 選擇**網站**的平台，為，然後按一下**[略過與建立應用程式識別碼**。

    ![Facebook-新增新的應用程式](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![Facebook-新增新的應用程式-網站](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![Facebook-建立應用程式識別碼](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. 在表單上，提供資料**的顯示名稱**、 適當的**類別**，有效的**連絡人電子郵件**，然後按一下 [**建立應用程式識別碼**]。 這需要您以接受 Facebook 平台原則，並完成線上安全性核取。

    ![Facebook-建立新的應用程式識別碼](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. 在左側導覽中按一下 [**設定**]。
6. 按一下 [ **+ 新增平台**，然後選取 [**網站**。

    ![Facebook-設定](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![Facebook-設定-網站](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. 在 [**網站 URL** ] 欄位中輸入[https://login.microsoftonline.com/](https://login.microsoftonline.com/) ，然後按一下 [**儲存變更**。

    ![Facebook 的網站 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

8. 將複製的**應用程式識別碼**值。 按一下 [**顯示**]，然後複製的**應用程式密碼**值。 您將需要兩個即可將 Facebook 設定為在您的租用戶中的身分識別提供者。 **應用程式密碼**是重要的安全性認證。

    ![Facebook-應用程式識別碼與應用程式密碼](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)

9. 按一下 [ **+ 新增產品**在左側導覽畫面中，然後**Facebook 登入**] 旁的 [**開始**] 按鈕上。

    ![Facebook-Facebook 登入](./media/active-directory-b2c-setup-fb-app/fb-login.png)

10. 輸入`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`**有效 OAuth 重新導向 Uri** ] 欄位在**用戶端 OAuth 設定**] 區段中。 **{租用戶}**換成您的租用戶名稱 (例如，contosob2c.onmicrosoft.com)。 按一下 [**儲存變更**，底部的頁面]。

    ![Facebook-OAuth 重新導向 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

11. 若要讓您的 Facebook 應用程式可用來 Azure AD B2C，您需要讓可公開使用。 在左側導覽中按一下 [**應用程式檢閱**並開啟切換為 [**是]**頁面的頂端，按一下 [**確認**您可以這麼做。

    ![Facebook-應用程式公用](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>將 Facebook 設定為在您的租用戶中的身分識別提供者

1. 請遵循下列步驟，[瀏覽至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)Azure 入口網站上。
2. 在 B2C 功能刀中，按一下 [**身分識別提供者**。
3. 按一下 [ **+ 新增**刀頂端]。
4. 提供身分識別提供者設定好記的**名稱**。 例如，輸入 「 明體]。
5. 按一下 [**身分識別提供者類型**，選取 [ **Facebook**]，按一下**[確定]**。
6. 按一下 [**設定此身分識別提供者**，輸入應用程式識別碼和應用程式密碼 （的您先前建立的 Facebook 應用程式） 分別在**用戶端識別碼**] 與 [**用戶端密碼**欄位。
7. 按一下 [**確定**]，然後按一下 [**建立**]，儲存您的 Facebook 設定。
