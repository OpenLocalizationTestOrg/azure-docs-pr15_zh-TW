<properties
    pageTitle="Azure 多重因素驗證 azure MFA 登入體驗"
    description="此頁面會提供您指導方針移至 [請參閱 Azure MFA 提供各種不同的登入方法的位置。"
    keywords="使用者驗證、 登入體驗，使用行動電話，登入登入 office 電話"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="the-sign-in-experience-with-azure-multi-factor-authentication"></a>登入經驗 Azure 多重因素驗證
> [AZURE.NOTE]  在此頁面上提供的下列文件會顯示一般的登入體驗。  登入方面的說明請參閱[Azure 多重因素驗證有方面的問題](multi-factor-authentication-end-user-manage-settings.md)



## <a name="what-will-your-sign-in-experience-be"></a>您的登入體驗是什麼？
如何登入和使用多重因素驗證，根據您的體驗會不同。  本節中，我們會提供資訊在您登入時發生的事。  選擇最能描述您所執行的動作︰


做什麼？|描述
:------------- | :------------- |
[使用行動電話] 或 [辦公室電話登入](#signing-in-with-mobile-or-office-phone) | 這是您可以預期無法登入使用行動電話] 或 [辦公室電話。
[登入 Microsoft 驗證應用程式使用通知](#signing-in-with-the-microsoft-authenticator-app-using-notification) | 這是您可以預期的事情使用 Microsoft 驗證應用程式通知。
[登入 Microsoft 驗證應用程式使用驗證碼](#signing-in-with-the-microsoft-authenticator-app-using-verification-code)|這是您可以預期的事情 Microsoft 驗證 thapp 使用驗證碼。
[登入的替代方法](#signing-in-with-an-alternate-method)|這會顯示您發生的事如果您想要使用的替代方法。

## <a name="signing-in-with-mobile-or-office-phone"></a>使用行動電話] 或 [辦公室電話登入

下列資訊將會說明使用多重因素驗證您的行動電話] 或 [辦公室電話的體驗。

### <a name="to-sign-in-with-a-call-to-your-office-or-mobile-phone"></a>登入至您的 office 或行動電話的通話

- 應用程式或服務，例如 Office 365 使用您的使用者名稱和密碼登入。
- Microsoft 會撥打電話給您。

![Microsoft 通話](./media/multi-factor-authentication-end-user-signin-phone/call.png)

- 接聽電話，並按 # 鍵。

![Answer （回應)](./media/multi-factor-authentication-end-user-signin-phone/phone.png)

- 您現在應該登入。</li>

## <a name="signing-in-with-the-microsoft-authenticator-app-using-notification"></a>登入 Microsoft 驗證應用程式使用通知

下列資訊將會說明您傳送的通知，使用 Microsoft 驗證應用程式使用多重因素驗證的體驗。

### <a name="to-sign-in-with-a-notification-sent-the-microsoft-authenticator-app"></a>若要登入通知傳送 Microsoft 驗證應用程式

- 應用程式或服務，例如 Office 365 使用您的使用者名稱和密碼登入。
- Microsoft 會傳送通知。

![Microsoft 會傳送通知](./media/multi-factor-authentication-end-user-signin-app-notify/notify.png)


- 接聽電話，然後按下 [驗證] 鍵。  如果您的公司需要 PIN 將會要求您為其以下。

![驗證](./media/multi-factor-authentication-end-user-signin-app-notify/phone.png)

![設定](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

- 您現在應該登入。


## <a name="signing-in-with-the-microsoft-authenticator-app-using-verification-code"></a>登入 Microsoft 驗證應用程式使用驗證碼

下列資訊將會說明時使用的驗證碼，使用 Microsoft 驗證應用程式使用多重因素驗證的體驗。

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>若要使用的驗證碼與 Microsoft 驗證應用程式登入

- 應用程式或服務，例如 Office 365 使用您的使用者名稱和密碼登入。
- Microsoft 會提示您輸入驗證碼。

![輸入驗證碼](./media/multi-factor-authentication-end-user-signin-app-verify/verify.png)

- 開啟手機的 Microsoft 驗證應用程式，並在您的登入] 方塊中輸入密碼。

![取得程式碼](./media/multi-factor-authentication-end-user-signin-app-verify/phone.png)



- 您現在應該登入。


## <a name="signing-in-with-an-alternate-method"></a>登入的替代方法


[動作] 區段會顯示如何登入的替代方法，當您主要的方法可能無法使用。

### <a name="to-sign-in-with-an-alternate-method"></a>若要使用的替代方法登入

- 應用程式或服務，例如 Office 365 使用您的使用者名稱和密碼登入。
- 選取 [使用不同的驗證選項。  您將會出現在選擇不同的選項。 數字您看到會根據您有安裝數。

![使用替代的方法](./media/multi-factor-authentication-end-user-signin-alt/alt.png)

- 選擇其他方法，並登入。
