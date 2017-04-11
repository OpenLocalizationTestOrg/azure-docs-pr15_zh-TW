<properties
    pageTitle="Azure AD 網域服務︰ 啟用密碼同步處理 |Microsoft Azure"
    description="快速入門 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/20/2016"
    ms.author="maheshu"/>

# <a name="enable-password-synchronization-to-azure-ad-domain-services"></a>啟用密碼同步處理至 Azure AD 網域服務
上述的基本工作，您可以啟用 Azure AD 租用戶的 Azure AD 網域服務。 下一步是啟用 NTLM 和 Kerberos 驗證，要同步處理到 Azure AD 網域服務所需的認證雜湊。 認證同步處理設定時，使用者可以為受管理的網域使用其公司的認證登入。

相關的步驟有不同根據您的組織是否有雲端專用的 Azure AD 租用戶，或設為與使用 Azure AD Connect 您內部部署目錄同步處理。

<br>

> [AZURE.SELECTOR]
- [雲端專用 Azure AD 租用戶](active-directory-ds-getting-started-password-sync.md)
- [同步處理 Azure AD 租用戶](active-directory-ds-getting-started-password-sync-synced-tenant.md)

<br>


## <a name="task-5-enable-password-synchronization-to-aad-domain-services-for-a-cloud-only-azure-ad-tenant"></a>任務 5︰ 啟用密碼同步處理 AAD 網域服務的雲端專用 Azure AD 租用戶
Azure AD 網域服務需要認證雜湊適合 NTLM 和 Kerberos 驗證]，以驗證使用者的受管理的網域上格式。 除非您啟用 AAD 網域服務租用戶，Azure AD 就不會產生或儲存認證雜湊 NTLM 或 Kerberos 驗證所需的格式。 基於明顯的安全性，Azure AD 也不會儲存任何認證純文字形式。 因此，Azure AD 沒有方式，以產生這些的 NTLM 或 Kerberos 認證雜湊根據使用者的現有的認證。

> [AZURE.NOTE] 如果貴組織已有雲端專用 Azure AD 租用戶，需要使用 Azure AD 網域服務的使用者必須變更其密碼。

此密碼變更程序會使雜湊 Kerberos 和 NTLM 驗證 Azure AD 中可產生 Azure AD 網域服務所需的認證。 您可以表示過期，必須使用 Azure AD 網域服務指示這些使用者變更其密碼的租用戶中的所有使用者的密碼。


### <a name="enable-ntlm-and-kerberos-credential-hash-generation-for-a-cloud-only-azure-ad-tenant"></a>啟用 NTLM 和 Kerberos 認證雜湊產生的雲端專用 Azure AD 租用戶
以下是您需要提供使用者，讓他們可以變更其密碼的相關指示︰

1. 瀏覽至您的組織在[http://myapps.microsoft.com](http://myapps.microsoft.com)Azure AD 存取面板頁面。

2. 選取 [在此頁面上的 [**設定檔**] 索引標籤。

3. 按一下 [在此頁面上的 [**變更密碼**] 方塊。

    ![Azure AD 網域服務中建立虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password.png)

    > [AZURE.NOTE] 如果您沒有看到 [存取面板] 頁面上的 [**變更密碼**] 選項，請確定您的組織已設定[密碼管理 Azure AD 中](../active-directory/active-directory-passwords-getting-started.md)。

4. 在 [**變更密碼**] 頁面中，輸入現有密碼 （舊版） 然後輸入新密碼，然後確認。 按一下 [**送出**]。

    ![Azure AD 網域服務中建立虛擬網路。](./media/active-directory-domain-services-getting-started/user-change-password2.png)

您已變更您的密碼之後，新的密碼會 Azure AD 網域服務中使用引進了。 在幾分鐘的時間之後 (通常是，關於 20 分鐘），您可以登入電腦加入受管理的網域使用新變更的密碼。

<br>

## <a name="related-content"></a>相關的內容

- [如何更新您自己的密碼](../active-directory/active-directory-passwords-update-your-own-password.md)

- [Azure AD 中的密碼管理快速入門](../active-directory/active-directory-passwords-getting-started.md)。

- [啟用密碼同步處理至 AAD 網域服務的同步處理的 Azure AD 租用戶](active-directory-ds-getting-started-password-sync-synced-tenant.md)

- [管理 Azure AD 網域服務管理的網域](active-directory-ds-admin-guide-administer-domain.md)

- [加入 Azure AD 網域服務受管理網域的 Windows 虛擬機器](active-directory-ds-admin-guide-join-windows-vm.md)

- [紅色角色企業 Linux 虛擬機器加入 Azure AD 網域服務受管理網域](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
