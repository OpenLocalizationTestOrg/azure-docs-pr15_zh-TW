<properties
    pageTitle="Azure Active Directory B2C: LinkedIn 設定 |Microsoft Azure"
    description="消費者與應用程式，受到 Azure Active Directory B2C LinkedIn 帳戶提供註冊和登入"
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

# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-linkedin-accounts"></a>Azure Active Directory B2C︰ 消費者與 LinkedIn 帳戶提供註冊和登入

## <a name="create-a-linkedin-application"></a>建立 LinkedIn 應用程式

若要使用 LinkedIn 的 Azure Active Directory (Azure AD) B2C 身分識別提供者，必須先建立一個 LinkedIn 應用程式，並提供正確的參數。 您需要執行此動作的 LinkedIn 帳戶。 如果您沒有帳戶，您可以在[https://www.linkedin.com/](https://www.linkedin.com/)取得它。

1. 移至 [ [LinkedIn 開發人員網站](https://www.developer.linkedin.com/)，並使用您的 LinkedIn 帳戶認證登入。
2. 按一下頂端的功能表列中的 [**我的應用程式**，然後按一下 [**建立應用程式**。

    ![LinkedIn 的新的應用程式](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. **建立新的應用程式**在表單中，填寫 （**公司名稱**、**名稱**、**描述**、**應用程式標誌 URL**、**應用程式**、**網站 URL**、**商務電子郵件**和**公司電話**） 的相關資訊。
4. 同意**LinkedIn API 使用條款**，然後按一下 [**送出**]。

    ![LinkedIn-Register 應用程式](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. **用戶端識別碼**] 和 [**用戶端密碼**的值，複製。 （您可以找到這些**驗證機碼**之下。）您將需要兩個即可將 LinkedIn 設定為在您的租用戶中的身分識別提供者。

    >[AZURE.NOTE] **用戶端祕**訣很重要的安全性認證。

6. 輸入`https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`（在下**OAuth 2.0**) 的**授權重新導向的 Url** ] 欄位中。 **{租用戶}**換成您的租用戶名稱 (例如 contoso.onmicrosoft.com)。 按一下 [**新增**]，然後按一下 [**更新**。 **{租用戶}**值是區分大小寫。

    ![LinkedIn-設定應用程式](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## <a name="configure-linkedin-as-an-identity-provider-in-your-tenant"></a>將 LinkedIn 設定為在您的租用戶中的身分識別提供者

1. 請遵循下列步驟，[瀏覽至 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)Azure 入口網站上。
2. 在 B2C 功能刀中，按一下 [**身分識別提供者**。
3. 按一下 [ **+ 新增**刀頂端]。
4. 提供身分識別提供者設定好記的**名稱**。 例如，輸入 「 平 」。
5. 按一下 [**身分識別提供者類型**，選取 [ **LinkedIn**]，按一下**[確定]**。
6. 按一下 [**設定此身分識別提供者**，輸入用戶端識別碼] 和 [用戶端您先前建立的 LinkedIn 應用程式的密碼。
7. 按一下**[確定]** ，然後按一下 [**建立**]，儲存您的 LinkedIn 設定。
