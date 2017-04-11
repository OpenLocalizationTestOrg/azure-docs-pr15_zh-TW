<properties
    pageTitle="Azure Active Directory B2C: Google + 設定 |Microsoft Azure"
    description="使用 Google + 帳戶受到 Azure Active Directory B2C 的應用程式中的客戶提供註冊和登入。"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C︰ 提供註冊和登入 Google + 帳戶與客戶

## <a name="create-a-google-application"></a>建立 Google + 應用程式

若要使用 Google + 中 Azure Active Directory (Azure AD) B2C 身分識別提供者，必須先建立一個 Google + 應用程式，並提供正確的參數。 您需要執行此動作的 Google + 帳戶。 如果您沒有帳戶，您可以在[https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)取得它。

1. 移至 [ [Google 開發人員主控台](https://console.developers.google.com/)，並使用您的 Google + 帳戶認證登入。
2. **建立專案**後，輸入**專案名稱**，然後按一下 [**建立**]。

    ![Google +-快速入門](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![Google +-新專案](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. 按一下 [ **API 管理員**，然後按一下左側導覽中的 [**認證**。
4. 按一下頂端的 [ **OAuth 同意螢幕**] 索引標籤。

    ![Google +-認證](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 選取或指定有效的**電子郵件地址**、 提供**產品名稱**，並按一下 [**儲存**]。

    ![Google +-OAuth 同意螢幕](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. 按一下 [**新的認證**，然後選擇 [ **OAuth 用戶端識別碼**。

    ![Google +-OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. 在 [**應用程式類型**] 底下，選取 [ **Web 應用程式**]。

    ![Google +-OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. 提供您的應用程式的**名稱**，請輸入`https://login.microsoftonline.com`**授權 JavaScript 來源**] 欄位中，在和`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`**已獲授權重新導向 Uri** ] 欄位中。 **{租用戶}**換成您的租用戶名稱 (例如，contosob2c.onmicrosoft.com)。 **{租用戶}**值是區分大小寫。 按一下 [**建立**]。

    ![Google +-建立用戶端識別碼](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. **用戶端識別碼**] 和 [**用戶端密碼**的值，複製。 您將需要兩個即可設定 Google + 為您的租用戶中的身分識別提供者。 **用戶端祕**訣很重要的安全性認證。

    ![Google +-用戶端密碼](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>設定 Google + 為您的租用戶中的身分識別提供者

1. 請遵循下列步驟，[瀏覽至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)Azure 入口網站上。
2. 在 B2C 功能刀中，按一下 [**身分識別提供者**。
3. 按一下 [ **+ 新增**刀頂端]。
4. 提供身分識別提供者設定好記的**名稱**。 例如，輸入 「 G + 」。
5. 按一下**身分識別提供者類型**、 選取**Google**，然後按一下**[確定]**。
6. 按一下 [**設定此身分識別提供者**，輸入用戶端識別碼] 和 [用戶端您先前建立的 Google + 應用程式的密碼。
7. 按一下**[確定]** ，然後按一下 [**建立**]，儲存您的 Google + 設定。
