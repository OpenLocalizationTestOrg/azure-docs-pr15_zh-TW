<properties
    pageTitle="Azure Active Directory B2C: Microsoft 帳戶設定 |Microsoft Azure"
    description="消費者與 Microsoft 帳戶，受到 Azure Active Directory B2C 應用程式中提供註冊和登入。"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C︰ 提供註冊和登入消費者與 Microsoft 帳戶

## <a name="create-a-microsoft-account-application"></a>建立 Microsoft 帳戶的應用程式

若要使用的 Microsoft 帳戶的 Azure Active Directory (Azure AD) B2C 身分識別提供者，必須先建立 Microsoft 帳戶的應用程式，並提供正確的參數。 您需要 Microsoft 帳戶來執行此動作。 如果您沒有帳戶，您可以在[https://www.live.com/](https://www.live.com/)取得它。

1. 前往 [ [Microsoft 應用程式註冊入口網站](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)，並使用您的 Microsoft 帳戶認證登入。
2. 按一下 [**新增應用程式**]。

    ![Microsoft 帳戶-新增新的應用程式](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)

3. 提供您的應用程式的**名稱**，然後按一下 [**建立應用程式**。

    ![Microsoft 帳戶的應用程式名稱](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)

4. 將複製的**應用程式識別碼**值。 您必須以 Microsoft 帳戶設定為在您的租用戶中的身分識別提供者。

    ![Microsoft 帳戶的應用程式識別碼](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)

5. 按一下 [**新增平台**上，然後選擇 [ **Web**]。

    ![Microsoft 帳戶-新增平台](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)

    ![Microsoft 帳戶-網頁](./media/active-directory-b2c-setup-msa-app/msa-web.png)

6. 輸入`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`**重新導向 Uri** ] 欄位中。 **{租用戶}**換成您的租用戶名稱 (例如，contosob2c.onmicrosoft.com)。

    ![Microsoft 帳戶-重新導向的 URL](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)

7. 在 [**應用程式密碼**] 區段底下，按一下 [上**產生新密碼**]。 複製顯示在螢幕上的新密碼。 您必須以 Microsoft 帳戶設定為在您的租用戶中的身分識別提供者。 此密碼不重要的安全性認證。

    ![Microsoft 帳戶-產生新的密碼](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)

    ![Microsoft 帳戶的新密碼](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)

8. 核取方塊，指出在 [**進階選項**] 區段底下的 [ **Live SDK 支援**。 按一下 [**儲存**]。

    ![Microsoft 帳戶-Live SDK 支援](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Microsoft 帳戶設定為在您的租用戶中的身分識別提供者

1. 請遵循下列步驟，[瀏覽至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)Azure 入口網站上。
2. 在 B2C 功能刀中，按一下 [**身分識別提供者**。
3. 按一下 [ **+ 新增**刀頂端]。
4. 提供身分識別提供者設定好記的**名稱**。 例如，輸入 「 MSA 」。
5. 按一下 [**身分識別提供者類型**，選取 [ **Microsoft 帳戶**]，按一下**[確定]**。
6. 按一下 [**設定此身分識別提供者**，輸入應用程式識別碼和密碼，您先前建立的 Microsoft 帳戶應用程式。
7. 按一下**[確定]** ，然後按一下 [**建立**]，儲存您的 Microsoft 帳戶設定。
