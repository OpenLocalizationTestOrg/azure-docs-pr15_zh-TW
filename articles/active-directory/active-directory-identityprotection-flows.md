<properties
    pageTitle="登入體驗 Azure AD 身分識別保護 |Microsoft Azure"
    description="降低或在於使用者身分識別保護或多重因素驗證所需的原則時，請提供使用者體驗的概觀。"
    services="active-directory"
    keywords="azure active directory 身分識別保護]，[管理應用程式、 安全性、 風險、 風險層級、 弱點、 安全性原則的雲端應用程式探索"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Azure AD 身分識別保護的登入體驗

Azure Active Directory 身分識別保護，您可以︰

- 要求使用者註冊使用多重因素驗證

- 高風險登增益集和洩漏的使用者控點

對這些問題的系統回應有會影響使用者的登入體驗，因為只要直接登入提供使用者名稱和密碼不會再。 執行額外步驟才能安全地取得使用者到商務用。

本主題可讓您概略瞭解使用者的登入體驗的所有可能會發生的情況。

**多重因素驗證**

- 多重因素驗證註冊



**登入風險**

- 高風險的登入復原

- 高風險登入封鎖

- 多重因素驗證註冊期間高風險的登入
 

**風險的使用者**

- 洩漏的帳戶復原

- 洩漏封鎖的帳戶




## <a name="multi-factor-authentication-registration"></a>多重因素驗證註冊

使用者可以自動復原時的兩個洩漏的帳戶復原流程及高風險登入流程，最佳的使用者體驗。 如果使用者已登錄多重因素驗證，已經有可以用來傳遞安全性的挑戰其帳戶相關聯的電話號碼。 沒有說明的電話機或系統管理員參與需要從帳戶洩漏復原。 因此，強烈建議您取得您註冊多重因素驗證的使用者。 

系統管理員可以︰

- 設定原則，要求使用者為自己的帳戶進行額外的安全性驗證設定。 
- 允許略過多因素驗證註冊，30 天內，以避免他們想要讓限期之前註冊的使用者。

**多重因素驗證註冊具有三個步驟︰**

1. 在第一個步驟中，使用者會取得相關設定多重因素驗證為帳戶要求通知。 

    ![補救](./media/active-directory-identityprotection-flows/140.png "修復")


2. 若要設定多重因素驗證，您需要讓知道您想要連絡的系統。

    ![修復](./media/active-directory-identityprotection-flows/141.png "修復")
 
3. 系統提交的挑戰您，您需要回應。

    ![修復](./media/active-directory-identityprotection-flows/142.png "修復")

 



## <a name="risky-sign-in-recovery"></a>高風險的登入復原

當系統管理員已設定原則登入的風險時，受影響的使用者會收到通知，他們嘗試登入。 

**高風險的登入流程具有兩個步驟︰** 

1. 特殊項目已偵測到相關的登入，例如從新的位置、 裝置或應用程式登入，會通知使用者。 

    ![補救](./media/active-directory-identityprotection-flows/120.png "修復")

2. 使用者需要將其識別身分來解決安全性挑戰。 如果使用者已註冊的多重因素驗證他們需要往返安全密碼給他們的電話號碼。 因為這是只高風險的登入和不洩漏的帳戶，使用者無法變更此流程中的密碼。 

    ![修復](./media/active-directory-identityprotection-flows/121.png "修復")



 
## <a name="risky-sign-in-blocked"></a>高風險登入封鎖
系統管理員，也可以選擇將登入風險原則設定為在登入根據風險層級的封鎖使用者。 若要取得解除封鎖，使用者必須連絡管理員或服務台，或他們可以嘗試從熟悉的位置或裝置登入。 不在此情況下一個選項自我復原來解決多重因素驗證。

![修復](./media/active-directory-identityprotection-flows/200.png "補救")




## <a name="compromised-account-recovery"></a>洩漏的帳戶復原

符合使用者的使用者時已設定使用者風險安全性原則，風險原則中指定的層級 (因此假設和遭到盜用) 之前，可以登入，必須經過使用者洩漏復原流程。 

**使用者洩漏復原流程具有三個步驟︰**

1. 帳戶安全性風險，因為可疑的活動或遺漏的認證，會通知使用者。

    ![修復](./media/active-directory-identityprotection-flows/101.png "補救")

2.  使用者需要將其識別身分來解決安全性挑戰。 如果使用者已註冊的多重因素驗證他們可以自動修復被盜用。 必須往返安全密碼給他們的電話號碼。 

    ![修復](./media/active-directory-identityprotection-flows/110.png "修復")


3.  最後，使用者會強制變更他們的密碼，因為其他人有其帳戶的存取權。 此體驗的螢幕擷取畫面如下。
 
    ![修復](./media/active-directory-identityprotection-flows/111.png "修復")



## <a name="compromised-account-blocked"></a>洩漏封鎖的帳戶 

若要取得已封鎖解除封鎖使用者風險安全性原則的使用者，使用者必須連絡系統管理員或服務台。 不在此情況下一個選項自動復原來解決多重因素驗證。


![補救](./media/active-directory-identityprotection-flows/104.png "補救")



 
## <a name="reset-password"></a>重設密碼

如果無法登入封鎖洩漏的使用者，以系統管理員可以的產生暫時密碼。 使用者必須在下一個登入期間變更他們的密碼。

![修復](./media/active-directory-identityprotection-flows/160.png "修復")


 




 

## <a name="see-also"></a>另請參閱

- [Azure Active Directory 身分識別保護](active-directory-identityprotection.md) 