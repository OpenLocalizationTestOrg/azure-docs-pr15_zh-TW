<properties
    pageTitle="管理您的兩個步驟驗證設定 |Microsoft Azure"
    description="管理您如何使用 Azure 多重因素驗證，包括變更您的連絡人資訊或設定您的裝置。"
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

# <a name="manage-your-settings-for-two-step-verification"></a>管理您的設定進行雙步驟驗證

本文將回答有關如何更新的兩個步驟驗證或多重因素驗證設定問題。 如果您有登入您的帳戶的問題，請參閱[有雙步驟驗證方面的問題](multi-factor-authentication-end-user-troubleshoot.md)的疑難排解說明。


## <a name="where-to-find-the-settings-page"></a>若要尋找 [設定] 頁面的位置
根據您的公司設定 Azure 多重因素驗證，有幾個位置您可以變更您的設定，例如您的電話號碼的位置。

如果您的 IT 系統管理員送出特定的 URL 或步驟，來管理雙步驟驗證，請遵循這些指示。 否則，下列指示應該適用於其他每一個人。 如果您請遵循下列步驟，但沒有看到相同的選項，這表示您的公司或學校自訂自己入口網站。 您 Azure 多重因素驗證入口網站的連結，請要求管理員。


1. 登入[https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. 在頂端，選取 [**設定檔**。  
3. 選取**額外的安全性驗證**。  

    ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. 其他安全性驗證頁面載入您的設定。

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>我要變更我的電話號碼，或新增第二個電話號碼

請務必設定第二個驗證電話號碼。  因為主要的電話號碼和您的行動應用程式可能是在相同的電話上，第二個電話號碼是唯一的方法，您可以返回至您的帳戶如果遺失或竊取您的電話。

> [AZURE.NOTE]
> 如果您沒有有權存取您的主要的電話號碼，並需要協助您開始您的帳戶，請參閱我們的說明主題中[有兩個步驟驗證方面的問題](multi-factor-authentication-end-user-troubleshoot.md)。

**若要變更您的主要的電話號碼︰**  

1. 在其他安全性驗證頁面上，選取 [文字] 方塊與您目前的電話號碼及編輯與您的新電話號碼。  
2. 選取 [**儲存**]。  
3. 如果這是您用於您慣用的驗證選項的數字，您必須驗證您可以將其儲存新的號碼。  


**若要新增的第二個電話號碼︰**  

1. 在其他安全性驗證頁面上，核取方塊下一步**替代驗證電話。**  
2. 在 [文字] 方塊中輸入您的第二個電話號碼。  
3. 完成**儲存**選取與您的變更。  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>如何從我的舊裝置清理 Microsoft 驗證，移至新的？
當您從您的裝置解除安裝應用程式或重設裝置時，它不會移除後端啟動。 您應該使用中[移至新裝置](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app)所述的步驟。

## <a name="next-steps"></a>後續步驟
- 取得疑難排解提示，並[有雙步驟驗證方面的問題](multi-factor-authentication-end-user-troubleshoot.md)的說明
- 設定[應用程式密碼](multi-factor-authentication-end-user-app-passwords.md)不支援雙步驟驗證任何應用程式。
