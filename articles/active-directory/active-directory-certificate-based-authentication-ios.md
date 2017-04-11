<properties 
    pageTitle="開始使用 iOS 上的憑證驗證 |Microsoft Azure" 
    description="瞭解如何設定 iOS 裝置中解決方案憑證驗證" 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/21/2016" 
    ms.author="markvi" />



# <a name="get-started-with-certificate-based-authentication-on-ios---public-preview"></a>開始使用 iOS-公用預覽上的憑證驗證

> [AZURE.SELECTOR]
- [iOS](active-directory-certificate-based-authentication-ios.md)
- [Android](active-directory-certificate-based-authentication-android.md)


本主題說明如何設定和使用 Office 365 企業版、 企業版和教育版方案的憑證驗證 (CBA) 的使用者在 iOS 裝置上的租用戶。 

CBA 可讓您連線至 Exchange online 帳戶時的 Azure Active Directory 驗證在 Android 或 iOS 裝置上的用戶端憑證︰ 

- Office 行動應用程式，例如 Microsoft Outlook 與 Microsoft Word   
- Exchange ActiveSync (EAS) 用戶端 

設定此功能，就不需要在行動裝置上輸入使用者名稱和密碼組合特定的郵件和 Microsoft Office 應用程式。 
 

## <a name="supported-scenarios-and-requirements"></a>支援的案例與需求  



### <a name="general-requirements"></a>一般需求 


本主題中的所有案例，都需要下列工作︰  

- 發行憑證的用戶端憑證 authority(s) 存取。  

- Azure Active Directory 中，必須設定憑證 authority(s)。 您可以尋找如何完成[快速入門](#getting-started)] 區段中的設定的詳細的步驟。  

- 根憑證授權單位，任何中繼憑證授權單位，都必須設定 Azure Active Directory 中。  

- 每個憑證授權單位必須擁有憑證撤銷清單 (CRL)，可以透過網際網路面對 URL 參考。  

- 用戶端憑證必須發行的用戶端驗證]。  


- Exchange ActiveSync 用戶端只，用戶端憑證必須擁有使用者的可傳送電子郵件地址在 Exchange online 中主體名稱或 RFC822 名稱主體替代名稱] 欄位的值。 Azure Active Directory 的目錄中的 Proxy 位址屬性對應 RFC822 值。  



### <a name="office-mobile-applications-support"></a>Office 行動應用程式支援 

| 應用程式                      | 支援      |
| ---                       | ---          |
| Word / Excel / PowerPoint | ![核取][1]  |
| OneNote                   | ![核取][1]  |
| OneDrive                  | ![核取][1]  |
| Outlook                   | ![核取][1]  |
| Yammer                    | ![核取][1]  |
| 商務用 Skype        | 即將推出  |


### <a name="requirements"></a>需求  

裝置作業系統版本必須是 iOS 9 以上 

必須設定同盟伺服器。  

Azure 驗證，才能在 iOS 上的 Office 應用程式。  

Azure Active Directory 撤銷的用戶端憑證的 ADFS 權杖必須具備下列宣告︰  

  - `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
（用戶端憑證的序列值） 

  - `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
（用戶端憑證的發行者的字串） 

如果他們可在 ADFS 權杖 （或任何其他 SAML 權杖），則 azure Active Directory 會重新整理權杖這些宣告。 重新整理權杖需要驗證，這項資訊會用來檢查撤銷。 

最佳作法是，您應該以下列更新 ADFS 錯誤頁面︰

- 在 iOS 上安裝 Azure 驗證需求

- 瞭解如何取得使用者憑證的相關指示。 

如需詳細資訊，請參閱[自訂 AD FS 登入頁面](https://technet.microsoft.com/library/dn280950.aspx)。  



### <a name="exchange-activesync-clients-support"></a>Exchange ActiveSync 用戶端支援 


在 iOS 9 或更新版本，支援的原生 iOS 郵件用戶端。 所有其他 Exchange ActiveSync 應用程式，來判斷是否支援此功能，請連絡您的應用程式開發人員。  



## <a name="getting-started"></a>快速入門 


若要開始，您需要設定 Azure Active Directory 中的憑證授權單位。 為每個憑證授權單位上, 傳下列動作︰ 

- 憑證， *.cer*格式公開金鑰的部分 

- 網際網路對 Url 憑證撤銷清單 (Crl) 所在的位置
 

以下是憑證授權單位的結構描述︰ 

    class TrustedCAsForPasswordlessAuth 
    { 
       CertificateAuthorityInformation[] certificateAuthorities;    
    } 

    class CertificateAuthorityInformation 

    { 
        CertAuthorityType authorityType; 
        X509Certificate trustedCertificate; 
        string crlDistributionPoint; 
        string deltaCrlDistributionPoint; 
        string trustedIssuer; 
        string trustedIssuerSKI; 
    }                

    enum CertAuthorityType 
    { 
        RootAuthority = 0, 
        IntermediateAuthority = 1 
    } 


若要上傳資訊，您可以使用透過 Windows PowerShell 的 Azure AD 模組。  
以下是新增、 移除或修改憑證授權單位的範例。 



### <a name="configuring-your-azure-ad-tenant-for-certificate-based-authentication"></a>設定您的 Azure AD 租用戶的憑證架構驗證 

1. 啟動 Windows PowerShell 具有管理員權限。 

2. 安裝 Azure AD 模組。 您必須安裝版本[1.1.143.0](http://www.powershellgallery.com/packages/AzureADPreview/1.1.143.0)或更新版本。  

        Install-Module -Name AzureADPreview –RequiredVersion 1.1.143.0 

3. 連線到您的目標租用戶︰ 

        Connect-AzureAD 

### <a name="adding-a-new-certificate-authority"></a>新增新的憑證授權單位

1. 設定不同的憑證授權單位的屬性，並將其新增至 Azure Active Directory: 

        $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]" 
        $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation 
        $new_ca.AuthorityType=0 
        $new_ca.TrustedCertificate=$cert 
        New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca 

5. 取得憑證授權單位︰ 

        Get-AzureADTrustedCertificateAuthority 


### <a name="retrieving-the-list-certificate-authorities"></a>擷取清單的憑證授權單位

擷取目前租用戶儲存 Azure Active Directory 中的憑證授權單位︰ 

        Get-AzureADTrustedCertificateAuthority 


### <a name="removing-a-certificate-authority"></a>移除憑證授權單位

1.  擷取憑證授權單位︰ 

        $c=Get-AzureADTrustedCertificateAuthority 


2. 移除憑證授權單位的憑證︰ 

        Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2] 


### <a name="modfiying-a-certificate-authority"></a>Modfiying 憑證授權單位 

1.  擷取憑證授權單位︰ 

        $c=Get-AzureADTrustedCertificateAuthority 


2. 修改憑證授權單位的屬性︰ 

        $c[0].AuthorityType=1 

3. 設定**憑證授權單位**︰ 

        Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0] 




## <a name="testing-office-mobile-applications"></a>測試 Office 行動應用程式  

若要測試憑證驗證]，在您行動裝置的 Office 應用程式︰ 

1.  在您測試的裝置，請從 App Store 安裝 Office 行動應用程式 (例如 OneDrive)。

2.  確認使用者憑證具有佈建到您測試的裝置。 

3.  啟動應用程式。 

4.  輸入您的使用者名稱，然後挑選您想要使用的使用者憑證。 

您應該順利登入。 





## <a name="testing-exchange-activesync-client-applications"></a>測試 Exchange ActiveSync 用戶端應用程式

若要存取 Exchange ActiveSync 透過基礎的憑證驗證]，其中包含用戶端憑證的 EAS 設定檔必須是應用程式使用。 EAS 設定檔必須包含下列資訊︰

- 若要使用於驗證使用者憑證 

- EAS 端點必須是 outlook.office365.com，（如只在 Exchange online 多租用戶環境中目前支援此功能）

EAS 設定檔可與放到 MDM，例如 Intune 或手動放在裝置上 EAS 設定檔中的 [憑證的裝置上。  

### <a name="testing-eas-client-applications-on-ios"></a>測試 EAS 用戶端應用程式在 iOS 上 

若要測試憑證驗證原生的郵件應用程式在 iOS 9 或上方︰ 

1.  設定 EAS 設定檔，符合上述的需求。 

2.  （使用 MDM，例如 Intune 或 Apple 設定程式應用程式） 的 iOS 裝置上安裝設定檔

3.  正確安裝設定檔之後，開啟原始郵件應用程式，並確認郵件已同步處理



## <a name="revocation"></a>撤銷

若要撤銷用戶端憑證，Azure Active Directory 從憑證授權單位資訊屬於上傳 Url 擷取憑證撤銷清單 (CRL)，然後將其快取。 上次發佈 CRL 中的時間戳記 （[**起算日期**] 屬性） 用來確保 CRL 是否仍然有效。 CRL 定期參照撤銷存取權的清單部分的憑證。

如果 （例如，如果使用者失去裝置） 需要更多立即撤銷，就可以失效使用者的授權權杖。 若要使授權權杖，設定**StsRefreshTokenValidFrom**欄位使用 Windows PowerShell 此特定使用者。 您必須更新**StsRefreshTokenValidFrom**欄位，為每個您想要撤銷的存取權的使用者。
 
若要確保撤銷仍然存在，您必須設定 CRL**起算日期**的日期值**StsRefreshTokenValidFrom**設定，並確保憑證後問題位於 CRL。
 
下列步驟建立大綱更新與設定**StsRefreshTokenValidFrom**欄位失效授權權杖的程序。 

1. 使用管理員認證 MSOL 服務的連線︰ 

        $msolcred = get-credential 
        connect-msolservice -credential $msolcred 

1.  擷取使用者的目前 StsRefreshTokensValidFrom 值︰ 

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com` 
        $user.StsRefreshTokensValidFrom 


1.  目前的時間戳記使用者等於設定新的 StsRefreshTokensValidFrom 值︰ 

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")


必須在未來您設定的日期。 如果日期不是在未來，未設定**StsRefreshTokensValidFrom**屬性。 如果是未來的日期， **StsRefreshTokensValidFrom**會設定為目前的時間 （而非日期設定 MsolUser] 命令以表示）。 



<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png