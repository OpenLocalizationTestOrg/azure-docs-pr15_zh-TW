<properties
    pageTitle="疑難排解雙步驟驗證 |Microsoft Azure"
    description="這份文件，其中提供使用者資訊如果它們遇到 Azure 多重因素驗證發生問題，該怎麼辦。"
    services="multi-factor-authentication"
    keywords = "多因素驗證的用戶端，驗證問題相互關聯識別碼"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="having-trouble-with-two-step-verification"></a>有兩個步驟驗證方面的問題

本文將說明您可能遇到的雙步驟驗證的一些問題。 如果您遇到的問題不包含以下，請提供詳細註解] 區段中的意見反應，協助我們改進。

## <a name="i-lost-my-phone-or-it-was-stolen"></a>我遺失我的電話，或它竊取

有兩種方式若要返回您的帳戶。 首先，若要使用登入您其他驗證的電話號碼，如果您已經設定一個。 第二個會要求您的系統管理員，以清除您的設定。

如果您的電話已遺失或竊取，我們也建議您擁有您的系統管理員重設您的應用程式密碼，然後清除任何記住裝置。 如果您的系統管理員不如何達到這個目的確定，請指引他們參考這篇文章︰[管理使用者和裝置](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords)。


### <a name="use-an-alternate-phone-number"></a>使用替代電話號碼

如果您已設定多個驗證選項，包括第二個電話號碼或驗證應用程式在不同的裝置，您可以使用其中一種登入。

若要登入使用的電話號碼，請遵循下列步驟︰

1. 像平常一樣，請登入。
2. 當畫面提示您進一步驗證您的帳戶，請選擇 [**使用不同的驗證選項**]。

    ![不同的驗證](./media/multi-factor-authentication-end-user-manage/differentverification.png)

3. 選取您有權存取的電話號碼。

    ![其他電話](./media/multi-factor-authentication-end-user-manage/altphone2.png)

4. 當您要變更您驗證的電話號碼後回您的帳戶，[管理您的設定](multi-factor-authentication-end-user-manage-settings.md)。

>[AZURE.IMPORTANT]
>請務必設定第二個驗證電話號碼。 如果您的主要的電話號碼和您的行動應用程式是相同的電話，您需要第三個選項，如果遺失或竊取您的電話。

### <a name="clear-your-settings"></a>清除您的設定

如果您未設定第二個驗證的電話號碼，然後您就必須連絡您的系統管理員以取得協助。 讓他們清除您的設定，以便在下次登入時，您會提示您[設定您的帳戶](multi-factor-authentication-end-user-first-time.md)一次。


## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>我無法接收簡訊或撥打我的手機上

有幾個原因，您可以嘗試登入，但是不會收到的文字或電話。 如果您已成功收到文字或行動電話至您的手機過去的然後這可能是電話提供者，而不是您的帳戶發生問題。 請確定您有很好的儲存格訊號，而且如果您想要接收文字訊息確認您的電話和服務方案支援文字訊息。

如果您已等候幾分鐘的文字或通話的時間，最快的方法，可將您的帳戶是要嘗試不同的選項。

1. 在正在等候您驗證的頁面上選取**使用不同的驗證選項**。

    ![不同的驗證](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)

2. 選取您想要使用的電話號碼或傳遞方式。

    如果您收到多個驗證碼，可搭配使用最新項目。

如果您沒有設定的另一種方法，請連絡您的管理員，並要求他們清除您的設定。 在您下次登入時，您將會提示您[設定多重因素驗證](multi-factor-authentication-end-user-first-time.md)一次。


如果您通常會有不正確的儲存格訊號造成的延遲，我們建議您在您的智慧型手機上使用[Microsoft 驗證應用程式](multi-factor-authentication-microsoft-authenticator.md)。 應用程式可以產生登入，您使用的隨機安全性代碼，這些代碼不需要任何儲存格訊號或網際網路連線。


## <a name="app-passwords-are-not-working"></a>應用程式密碼無法運作

首先，請確定您所輸入的應用程式密碼正確。  如果仍然無法運作，請嘗試登入，並[建立新的應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)。  如果這不使用連絡您的系統管理員，他們[刪除現有的應用程式密碼](multi-factor-authentication-manage-users-and-devices.md#delete-users-existing-app-passwords)，然後您可以建立一個新。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>我找不到我的問題的解答。

如果您已嘗試這些疑難排解步驟，但執行發生問題，請連絡您的系統管理員或設定多重因素驗證您的人員。 他們可以協助您。

此外，您可以將問題張貼在[Azure AD 論壇](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD)或[連絡支援人員](https://support.microsoft.com/contactus)，我們可以為回應問題。

如果您要連絡支援人員，包括下列資訊︰

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
- [管理您的設定進行雙步驟驗證](multi-factor-authentication-end-user-manage-settings.md)  
- [Microsoft 驗證應用程式常見問題集](multi-factor-authentication-app-faq.md)
