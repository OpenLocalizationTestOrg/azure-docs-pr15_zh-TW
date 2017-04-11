<properties
    pageTitle="Azure Active Directory B2C: Amazon 設定 |Microsoft Azure"
    description="消費者與應用程式，受到 Azure Active Directory B2C Amazon 帳戶提供註冊和登入。"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C︰ 消費者 Amazon 帳戶提供註冊和登入

## <a name="create-an-amazon-application"></a>建立 Amazon 應用程式

若要使用的身分識別提供者中 Azure Active Directory (Azure AD) B2C Amazon，必須先建立 Amazon 應用程式，並提供正確的參數。 您必須使用 Amazon 帳戶才能執行此動作。 如果您沒有帳戶，您可以在[http://www.amazon.com/](http://www.amazon.com/)取得它。

1. 移至[Amazon 開發人員中心](https://login.amazon.com/)，並使用您的 Amazon 帳戶認證登入。
2. 如果您有不這麼做，按一下 [**註冊**，請依照開發人員註冊步驟，並接受原則。
3. 按一下 [**註冊新的應用程式**。

    ![註冊 Amazon 網站的新應用程式](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)

4. 提供的應用程式的資訊 （**名稱**、**描述**和**隱私權注意事項 URL**），然後按一下 [**儲存**]。

    ![提供註冊 Amazon 在新的應用程式的應用程式的資訊](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)

5. 在 [**網站設定**] 區段中，複製值的**用戶端識別碼**] 和 [**用戶端密碼**。 （您需要按一下 [**顯示 [私人**] 按鈕，請參閱此）。您需要兩個即可將 Amazon 設定為在您的租用戶中的身分識別提供者。 按一下 [] 區段底部的 [**編輯**]。 **用戶端祕**訣很重要的安全性認證。

    ![在 Amazon 新應用程式提供用戶端識別碼] 和 [用戶端密碼](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)

6. 輸入`https://login.microsoftonline.com`**允許 JavaScript 來源**] 欄位中， `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` **允許傳回 Url** ] 欄位中。 **{租用戶}**換成您的租用戶名稱 (例如 contoso.onmicrosoft.com)。 按一下 [**儲存**]。 **{租用戶}**值是區分大小寫。

    ![在 Amazon 新應用程式提供的 JavaScript 來源及傳回 Url](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>為您的租用戶中的身分識別提供者設定 Amazon

1. 請遵循下列步驟，[瀏覽至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)Azure 入口網站上。
2. 在 B2C 功能刀中，按一下 [**身分識別提供者**。
3. 按一下 [ **+ 新增**刀頂端]。
4. 提供身分識別提供者設定好記的**名稱**。 例如，輸入 「 Amzn 」。
5. 按一下**身分識別提供者類型**、 選取**Amazon**，然後按一下**[確定]**。
6. 按一下 [**設定此身分識別提供者**，輸入用戶端識別碼] 和 [用戶端您先前建立的 Amazon 應用程式的密碼。
7. 按一下**[確定]** ，然後按一下 [**建立**]，儲存您 Amazon 設定。
