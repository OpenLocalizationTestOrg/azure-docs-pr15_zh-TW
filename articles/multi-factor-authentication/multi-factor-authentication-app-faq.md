<properties
    pageTitle="Microsoft 驗證應用程式常見問題集"
    description="提供常見問題集和相關的 Microsoft 驗證應用程式和 Azure 多重因素驗證的解答的清單。"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar, librown"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator-application-faq"></a>Microsoft 驗證應用程式常見問題集

Microsoft 驗證應用程式取代 Azure 驗證應用程式，並使用 Azure 多重因素驗證時，建議使用的應用程式。 使用 Windows Phone、 Android 和 iOS 此應用程式。

## <a name="frequently-asked-questions"></a>常見問題集

- **Azure 驗證多重因素驗證與 Microsoft 帳戶的應用程式有何改變？**

    Microsoft 驗證應用程式會取代彼此這些應用程式。 升級至 Microsoft 驗證 azure 驗證。 如果您使用的多重因素驗證或 Microsoft 帳戶的應用程式，請安裝 Microsoft 驗證者，並再次新增您的帳戶。 請務必先完成您的帳戶新增至新的應用程式，再刪除舊的。

- **已使用 Microsoft 驗證應用程式的驗證碼。如何切換單鍵推播通知？**  

    登入，透過推入通知核准只適用於 Microsoft 帳戶，不適用於第三方帳戶，例如 Google 或 facebook 的連絡人。 公司或學校的 Microsoft 帳戶，您的組織可以選擇透過停用此選項。

    如果您使用 Microsoft 帳戶，您的個人帳戶，並想要切換至 [推入通知，必須先再次新增您的帳戶。 重新註冊裝置以搭配您的帳戶，並設定推播通知。  

    如果您的帳戶沒有已啟用的雙步驟驗證，請參閱[關於雙步驟驗證](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)決定是否適合您。  

- **我何時能夠在 iPhone 或 iPad 上使用單鍵推播通知？**  

    此功能的 beta 直到最後一年 8 月，其變成人使用的 Microsoft 帳戶。 如果您想要加入我們 beta 程式時，傳送電子郵件至msauthenticator@microsoft.com。 在郵件中包含您的名字、 姓氏及 Apple ID。  

- **單鍵推入通知工作的非 Microsoft 帳戶？**  

    否，推入通知僅適用於用 Microsoft 帳戶和 Azure Active Directory 帳戶。 如果您的公司或學校使用 Azure AD 帳戶，它們可能會停用此功能。  

- **我從備份檔案，還原我的裝置，並我帳戶碼會遺失或無法運作。有何改變？**  

    基於安全性理由，我們不從應用程式備份還原的帳戶。 如果您是從備份檔案還原 iOS 應用程式，仍會顯示您的帳戶，但不會收到的驗證登入，或產生安全性代碼使用。 還原應用程式之後，刪除您的帳戶，然後再次新增。

- **我是新的裝置。如何從我的舊裝置移除 Microsoft 驗證應用程式，移至新的項目？**

    將 Microsoft 驗證應用程式新增至新的裝置並不會自動移除它從任何其他裝置。 若要管理您的帳戶設定的裝置，請造訪相同的網站，您可用於管理雙步驟驗證，並選擇要移除舊的應用程式。

    個人 Microsoft 帳戶，此網站是您[帳戶的安全性](https://account.microsoft.com/security)的頁面。 公司或學校的 Microsoft 帳戶，此網站可能是 [ [MyApps](https://myapps.microsoft.com) ] 或 [您的組織已設定的自訂入口網站。

## <a name="contact-us"></a>與我們連絡

如果此處未回答您的問題，會保留在頁面底部的註解。 或者，您也可以[連絡支援人員](https://support.microsoft.com/contactus)，我們會回應您的問題時我們可以。

如果您要連絡支援人員，包括的下列資訊，您可以︰

- **使用者識別碼**– 您嘗試登入的電子郵件位址是什麼？
- **錯誤的一般描述**– 哪些正確的錯誤訊息您是否看到？  如果有任何錯誤訊息，說明未預期的行為，您會在詳細資料。
- **頁面**-哪些頁面您是否曾經上時所看到的錯誤 （包含 URL）？
- **錯誤碼**-您收到的特定錯誤碼。
- **工作階段識別碼**-您收到的特定工作階段識別碼。
- **相互關聯識別碼**– 是使用者所看到的錯誤時產生的相互關聯識別碼程式碼。
- **時間戳記**– 是精確的日期和時間，您所看到的錯誤 （包含時區）？

此資訊可以登入] 頁面上找到。 當您不確認您登入時間時，請選取 [**檢視詳細資料**]。

![登入錯誤詳細資料](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

包括這項資訊可協助我們儘快解決的問題。

## <a name="related-topics"></a>相關的主題

- [Azure 多重因素驗證常見問題集](multi-factor-authentication-faq.md)  
- [雙步驟驗證相關](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)的 Microsoft 帳戶
- [無法使用雙步驟驗證嗎？](multi-factor-authentication-end-user-troubleshoot.md)
