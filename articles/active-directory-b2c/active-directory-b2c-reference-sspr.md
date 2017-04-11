<properties
    pageTitle="Azure Active Directory B2C︰ 自助密碼重設 |Microsoft Azure"
    description="示範如何設定自助密碼重設您的使用者能在 Azure Active Directory B2C 中的主題"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-set-up-self-service-password-reset-for-your-consumers"></a>設定自助密碼重設您的消費者用的 azure Active Directory B2C:

使用自助密碼重設功能，使用者 （已註冊的本機帳戶） 的使用者可以重設自己的密碼。 這會大幅降低負擔支援人員，尤其是如果應用程式的一般使用的消費者數百萬。 目前我們只支援使用驗證電子郵件地址做為復原方法。 我們會在未來加入額外的復原方法 （通過驗證的電話號碼、 安全性問題等）。

> [AZURE.NOTE]
本文適用於自助密碼重設登入原則的內容中使用。 如果您需要自訂密碼重設您的應用程式從叫用的原則，請參閱[本文](./active-directory-b2c-reference-policies.md#create-a-password-reset-policy)。

根據預設，您的目錄不會有自助密碼重設已開啟。 若要開啟使用下列步驟︰

1. 登入[Azure 傳統入口網站](https://manage.windowsazure.com/)以訂閱系統管理員。 這是相同的工作或學校帳戶或您用來建立目錄的相同 Microsoft 帳戶。
2. 瀏覽至左側導覽列上的 Active Directory 副檔名。
3. 尋找您的目錄，在 [**目錄**] 索引標籤，然後按一下它。
4. 按一下 [**設定**] 索引標籤。
5. 向下 [**重設使用者密碼原則**] 區段中捲動並之間切換**使用者啟用密碼重設**] 選項為 [**是]**。 請注意已核取 [**替代電子郵件地址**] 選項。保留原樣。

    ![自助密碼重設](./media/active-directory-b2c-reference-sspr/sspr.png)

6. 按一下 [在頁面底部的 [**儲存**]。 大功告成 ！

若要測試，使用 [立即執行] 功能在任何為身分識別提供者具有本機帳戶的登入原則。 在本機的帳戶登入] 頁面上 （在您輸入的電子郵件地址和密碼，或使用者名稱和密碼），請按一下**無法存取您的帳戶？**驗證的消費者體驗。

> [AZURE.NOTE]
使用[公司商標功能](../active-directory/active-directory-add-company-branding.md)可自訂的自助密碼重設頁面。
