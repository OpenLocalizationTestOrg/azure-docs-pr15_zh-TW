<properties
    pageTitle="憑證更新使用者準備的指引 Office 365 和 Azure Active Directory |Microsoft Azure"
    description="本文說明 Office 365 的使用者如何解決問題的相關更新憑證通知他們的電子郵件。"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="billmath"/>


# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>更新 Office 365 和 Azure Active Directory federation 憑證

##<a name="overview"></a>概觀

Azure Active Directory (Azure AD) 與 Active Directory Federation Services (AD FS) 之間的成功同盟，AD FS 用來登入安全性權杖 Azure ad 憑證應該相符 Azure AD 中的設定。 任何不相符，可能會導致中斷的信任。 Azure AD 可確保這項資訊會保持同步處理當您部署 AD FS 和 Web 應用程式 Proxy （適用於外部網路存取）。

本文提供其他資訊，來管理您的權杖簽署憑證，並將這些 Azure AD，同步在下列情況使用︰

* 您不想要部署 Web 應用程式 Proxy，因此同盟中繼資料不提供外部網路。
* 您不使用權杖簽署憑證 AD FS 的預設設定。
* 您正在使用協力廠商身分識別提供者。

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>AD FS 權杖簽署憑證的預設設定

權杖簽署並解密憑證的權杖通常自我簽署的憑證，而是一年。 根據預設，AD FS 包含稱為**AutoCertificateRollover**自動續約程序。 如果您使用 AD FS 2.0 或更新版本，Office 365 和 Azure AD 自動更新您的憑證之前續訂。

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>從 Office 365 入口網站或電子郵件的更新通知

>[AZURE.NOTE] 如果您收到電子郵件或入口網站通知詢問您的 Office 更新您的憑證，請參閱[管理] 變更為權杖簽署憑證](#managecerts)檢查您是否需要採取任何動作。 Microsoft 並知道傳送，即使不不需要任何動作的憑證更新的通知，可能會導致的可能問題。

Azure AD 嘗試監控同盟中繼資料，並更新權杖簽署憑證以這個中繼資料。 權杖簽署憑證的到期前 30 天 Azure AD 檢查新的憑證是否可用來輪詢同盟中繼資料。

* 如果可以順利投票同盟中繼資料並擷取新的憑證，沒有電子郵件通知] 或 [Office 365 入口網站中的警告會發出給使用者。
* 如果無法擷取新的權杖簽署憑證，可能是因為同盟中繼資料不到或尚未啟用自動憑證變換，Azure AD 問題電子郵件通知和 Office 365 入口網站中的警告。

![Office 365 入口網站的通知](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] 如果您使用 AD FS，以確保業務連續性，請確認您的伺服器有下列更新，以便驗證失敗的已知問題不會發生。 如此可減輕此更新和未來的更新期的已知的 AD FS proxy 伺服器問題︰
>
>Server 2012 R2- [Windows Server 2014 彙總套件](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 與 2012-[透過 proxy 驗證失敗的 Windows Server 2012 或 Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)

## 檢查是否需要更新憑證<a name="managecerts"></a>

### <a name="step-1-check-the-autocertificaterollover-state"></a>步驟 1︰ 檢查 AutoCertificateRollover 狀態

在您 AD FS 伺服器上，開啟 PowerShell。 AutoCertificateRollover 值設為 True 的核取。

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] 如果您使用 AD FS 2.0，第一次執行新增 Pssnapin Microsoft.Adfs.Powershell。

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>步驟 2︰ 確認 AD FS 和 Azure AD 同步

在您 AD FS 伺服器上，開啟 Azure AD PowerShell 提示時，並連線到 Azure AD。

>[AZURE.NOTE] 您可以下載 PowerShell 的 Azure AD[以下](https://technet.microsoft.com/library/jj151815.aspx)。

    Connect-MsolService

核取 AD FS 和 Azure AD 信任內容指定網域中設定的憑證。

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![取得 MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

在這兩種輸出憑證碼相符，您的認證 Azure AD 同步。

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>步驟 3︰ 檢查您的認證是否即將到期

在取得 MsolFederationProperty 或取得 AdfsCertificate 輸出中，核取的 「 不後面] 底下的日期 如果日期小於 30 天辦公室，您應該採取的動作。

| AutoCertificateRollover | Azure AD 同步的憑證 | 同盟中繼資料為公開存取 | 有效 | 巨集指令 |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| [是] | [是] | [是] | - | 不需要任何動作。 請參閱[更新權杖簽署憑證自動](#autorenew)。 |
| [是] | 無  | - | 小於 15 天 | 立即更新。 請參閱[更新權杖簽署憑證以手動方式](#manualrenew)。 |
| 無 | - | - | 小於 30 天 | 立即更新。 請參閱[更新權杖簽署憑證以手動方式](#manualrenew)。 |

\[-] 並不重要

## 更新的權杖自動簽章憑證 （建議使用）<a name="autorenew"></a>

您不需要執行任何手動步驟，是否均為 true，下列其中一項︰
- 您已經部署 Web 應用程式 Proxy，可以啟用同盟中繼資料的存取從外部網路。
- 您正在使用 AD FS 預設的設定 （啟用 AutoCertificateRollover）。

檢查以下項目以確認您可以自動更新憑證。

**1.AD FS 屬性 AutoCertificateRollover 必須設為 True。** 這表示 AD FS 會自動產生新的權杖簽署及 token 解密憑證，、 之前舊的項目過期。

**2.AD FS 同盟中繼資料是公開存取。** 請檢查您的同盟中繼資料 （從公司網路） 公用網際網路上的電腦瀏覽至下列 URL 為公開存取︰


https:// (your_FS_name) /federationmetadata/2007-06/federationmetadata.xml

位置`(your_FS_name) `會取代您的組織使用，例如 fs.contoso.com 同盟服務主機名稱。  如果您可以同時請確認這些設定成功，您不需要執行任何動作。  

範例︰ https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## 更新的權杖手動簽章憑證<a name="manualrenew"></a>

您可以選擇要更新的權杖手動簽章憑證。 例如，下列情況可能會更好的手動更新︰
* 權杖簽署憑證是自我簽署的憑證。 最常見的原因是您的組織管理 AD FS 憑證註冊從組織的憑證授權單位。
* 網路安全性不允許設為公開可用的同盟中繼資料。

在下列情況下，每次您更新的權杖簽章憑證，您也必須更新您的 Office 365 網域使用的 PowerShell 命令，更新 MsolFederatedDomain。

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>步驟 1︰ 確認 AD FS 有新的權杖簽署憑證

**非預設設定**

如果您使用 AD FS （ **AutoCertificateRollover**設定為 [ **False**） 的非預設設定，您可能會使用自訂的憑證 （不是自我簽署）。 如需有關如何更新 AD FS 權杖簽署憑證的詳細資訊，請參閱[不使用 AD FS 的客戶的指導方針自我簽署憑證](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert)。

**同盟中繼資料不是可公開使用**

相反地，如果**AutoCertificateRollover**設為**True**，但您同盟中繼資料不可以公開存取，首先請確定 AD fs 的已產生新的權杖簽署憑證。 確認您有新的權杖簽署憑證以採取下列步驟︰

1. 確認您的登入主要 AD FS server。
2. 檢查目前的簽章憑證 AD FS 中開啟 PowerShell 命令視窗中，執行下列命令︰

    PS c:\>取得 ADFSCertificate – CertificateType 權杖簽署

    >[AZURE.NOTE] 如果您使用 AD FS 2.0，您應該先執行新增 Pssnapin Microsoft.Adfs.Powershell。

3. 查看命令輸出在任何列出的憑證。 如果 AD FS 具有產生新的憑證，您應該會看到輸出中的兩個憑證︰ **IsPrimary**值為**True** ，以及**NotAfter**日期位於 5 天，並**IsPrimary**為**False** ，且**NotAfter**是在未來一年。

4. 如果您只看到一個憑證，且**NotAfter**日期 5 天內，您需要產生新的憑證。

5. 若要產生新的憑證，執行下列命令的 PowerShell 命令提示字元︰ `PS C:\>Update-ADFSCertificate –CertificateType token-signing`。

6. 再次執行下列命令確認更新︰ PS c:\>取得 ADFSCertificate – CertificateType 權杖簽署

現在應該會列出這兩個憑證，其中具有**NotAfter**大約一年未來的日期， **IsPrimary**值為**False**。

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>步驟 2︰ 更新新的權杖登入 Office 365 信任的憑證

更新 Office 365，以新的權杖簽署憑證用於信任]，如下所示。

1.  針對 Windows PowerShell 開啟 Microsoft Azure Active Directory 模組。
2.  執行 $cred = 取得認證。 當此指令程式會提示您輸入認證，請輸入您的雲端服務系統管理員帳戶認證。
3.  執行連線 MsolService – 認證 $cred。 這個指令程式可讓您連線到雲端服務。 建立連線至雲端服務的內容，都必須先執行任何的工具安裝的其他指令程式。
4.  如果您不是 AD FS 主要同盟伺服器的電腦上執行下列命令，執行設定 MSOLAdfscontext-電腦<AD FS primary server>，其中<AD FS primary server>是主要 AD FS server 內部 FQDN 名稱。 這個指令程式所建立的連線至 AD FS 的內容。
5.  執行更新 MSOLFederatedDomain-DomainName <domain>。 這個指令程式會更新到雲端服務，AD FS 的設定，並設定兩者之間的信任關係。


>[AZURE.NOTE] 如果您需要支援多個最上層網域的詳細資訊，例如 contoso.com 和 fabrikam.com，您必須使用任何 cmdlet 來**SupportMultipleDomain**切換。 如需詳細資訊，請參閱[支援的多個頂端層級的網域](active-directory-aadconnect-multiple-domains.md)。

## 使用 Azure AD Connect 修復 Azure AD 信任<a name="connectrenew"></a>

如果您使用 Azure AD Connect 設定您的 AD FS 伺服器陣列和 Azure AD 信任，您可以使用 Azure AD Connect 偵測如果您需要採取任何動作，您的權杖簽署憑證。 如果您需要更新憑證，您可以使用 Azure AD Connect 執行此作業。

如需詳細資訊，請參閱[修復信任](./active-directory-aadconnect-federation-management.md#repairing-the-trust)。
