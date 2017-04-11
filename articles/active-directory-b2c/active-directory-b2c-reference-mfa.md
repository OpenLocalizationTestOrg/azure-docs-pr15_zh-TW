<properties
    pageTitle="Azure Active Directory B2C︰ 多重因素驗證 |Microsoft Azure"
    description="如何在 Azure Active Directory B2C 受保護的消費者具應用程式中啟用多因素驗證"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure Active Directory B2C︰ 消費者具應用程式中啟用多因素驗證

Azure Active Directory (Azure AD) B2C 整合直接與[Azure 多重因素驗證](../multi-factor-authentication/multi-factor-authentication.md)，以便您可以新增第二層安全性註冊和登入體驗消費者具應用程式中。 然後，您可以不需撰寫程式碼。 目前我們支援電話和文字訊息驗證。 如果您已經建立註冊和登入的原則，您仍然可以啟用多重因素驗證。

> [AZURE.NOTE]
當您建立註冊和登入原則，而不只是由編輯現有的原則，也可以啟用多重因素驗證。

此功能可協助處理如下所示的案例的應用程式︰

- 您不需要多重因素驗證，存取應用程式，但您需要存取另一個。 例如，消費者可以登入使用社交或本機帳戶時，自動保險應用程式，但存取常用的保險應用程式登錄中相同的目錄之前，必須驗證的電話號碼。
- 您不需要多重因素驗證，存取應用程式，但您需要存取的機密的部分內。 例如，消費者可以登入社交或本機帳戶與核取的帳戶餘額的銀行應用程式，但必須驗證的電話號碼，然後再嘗試線上傳輸。

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>修改您註冊的原則，若要啟用多因素驗證

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**註冊原則**]。
3. 按一下您註冊的原則 (例如，「 B2C_1_SiUp 」)，將其開啟。
4. 按一下 [**多重因素驗證]** ，開啟 [**狀態** **]**。 按一下**[確定]**。
5. 按一下 [刀頂端的 [**儲存**]。

您可以使用 [立即執行] 功能的原則驗證的消費者體驗。 確認下列項目︰

多重因素驗證步驟舉行前消費者帳戶取得建立您的目錄。 在步驟中，消費者被要求提供他或她的電話號碼，並確認它。 如果驗證成功，以供日後使用的消費者帳戶附加的電話號碼。 即使消費者取消或卸除，他或她可以要求您確認過程中再次的下一個登入的電話號碼 （使用多重因素驗證啟用）。

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>修改您登入原則，若要啟用多因素驗證

1. [請遵循下列步驟，瀏覽至 Azure 入口網站上 B2C 功能刀](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. 按一下 [**登入原則**]。
3. 按一下您登入原則 (例如，「 B2C_1_SiIn 」)，將其開啟。 按一下 [刀頂端的 [**編輯**]。
4. 按一下 [**多重因素驗證]** ，開啟 [**狀態** **]**。 按一下**[確定]**。
5. 按一下 [刀頂端的 [**儲存**]。

您可以使用 [立即執行] 功能的原則驗證的消費者體驗。 確認下列項目︰

消費者時登入 （使用社交或本機帳戶），通過驗證的電話號碼附加到消費者帳戶時，如果要他或她要求，確認它。 如果附加電話號碼時，消費者會要求您提供一個，並確認其。 在成功驗證，以供日後使用的消費者帳戶連接的電話號碼。

## <a name="multi-factor-authentication-on-other-policies"></a>在其他原則上的多重因素驗證

註冊與登入原則上述的描述，也可啟用多重因素驗證上註冊或登入原則和密碼重設原則。 可在編輯原則的設定檔推出。
