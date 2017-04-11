<properties
    pageTitle="如何自動安裝 Azure AD 應用程式 Proxy 連接器 |Microsoft Azure"
    description="說明如何執行無 Azure AD 應用程式 Proxy 連接器，以提供安全遠端存取您內部部署的應用程式的安裝。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-silently-install-the-azure-ad-application-proxy-connector"></a>如何自動安裝 Azure AD 應用程式 Proxy 連接器

您要傳送的安裝指令碼至多個 Windows 伺服器或 Windows 伺服器沒有啟用的使用者介面。 本主題說明如何建立可安裝並註冊 Azure AD 應用程式 Proxy 連接器自動的安裝 Windows PowerShell 指令碼。

## <a name="enabling-access"></a>啟用存取
應用程式 Proxy 的運作方式安裝相機的 Windows Server 服務，稱為內部網路的連接器。 若要使用其應用程式 Proxy 連接器有註冊 Azure AD 目錄以全域管理員和密碼。 通常這是期間連接器安裝在快顯對話方塊中，輸入。 或者，您可以使用 Windows PowerShell 來建立的認證物件，輸入您的註冊資訊，或者您可以建立自己的權杖，並用來輸入您的註冊資訊。

## <a name="step-1--install-the-connector-without-registration"></a>步驟 1︰ 安裝不用註冊連接器


安裝連接器 Msi 不要註冊連接器，如下所示︰


1. 開啟命令提示字元。
2. 執行下列命令 /q 表示自動安裝-安裝不會提示您以接受使用者授權合約。

        AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q

## <a name="step-2-register-the-connector-with-azure-active-directory"></a>步驟 2︰ 使用 Azure Active Directory 登錄連接器
這可以使用下列方法之一來完成︰


- 註冊使用 Windows PowerShell 認證物件連接器
- 註冊使用離線時建立的權杖連接器

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>註冊使用 Windows PowerShell 認證物件連接器


1. 執行下列動作，以建立 Windows PowerShell 認證物件位置 」<username>」 和 「<password>」 應該使用使用者名稱和密碼，為您的目錄取代︰

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword

2. 移至**C:\Program Files\Microsoft AAD 應用程式 Proxy 連接器**與執行使用您所建立的 PowerShell 認證物件的指令碼，$cred 所在的 PowerShell 名稱認證您建立的物件︰

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred


### <a name="register-the-connector-using-a-token-created-offline"></a>註冊使用離線時建立的權杖連接器

1. 建立離線權杖使用 AuthenticationContext 類別使用中的程式碼片段的值︰


        using System;
        using System.Diagnostics;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

        class Program
        {
        #region constants
        /// <summary>
        /// The AAD authentication endpoint uri
        /// </summary>
        static readonly Uri AadAuthenticationEndpoint = new Uri("https://login.windows.net/common/oauth2/token?api-version=1.0");

        /// <summary>
        /// The application ID of the connector in AAD
        /// </summary>
        static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

        /// <summary>
        /// The reply address of the connector application in AAD
        /// </summary>
        static readonly Uri ConnectorRedirectAddress = new Uri("urn:ietf:wg:oauth:2.0:oob");

        /// <summary>
        /// The AppIdUri of the registration service in AAD
        /// </summary>
        static readonly Uri RegistrationServiceAppIdUri = new Uri("https://proxy.cloudwebappproxy.net/registerapp");

        #endregion

        #region private members
        private string token;
        private string tenantID;
        #endregion

        public void GetAuthenticationToken()
        {
            AuthenticationContext authContext = new AuthenticationContext(AadAuthenticationEndpoint.AbsoluteUri);

            AuthenticationResult authResult = authContext.AcquireToken(RegistrationServiceAppIdUri.AbsoluteUri,
                ConnectorAppId,
                ConnectorRedirectAddress,
                PromptBehavior.Always);

            if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
            {
                Trace.TraceError("Authentication result, token or tenant id returned are null");
                throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
            }

            token = authResult.AccessToken;
            tenantID = authResult.TenantId;
        }





2. 一旦您可以建立使用權杖 SecureString 的權杖︰ <br>
`$SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force`
3. 執行下列 Windows PowerShell 命令，其中 SecureToken 是您先前所建立的權杖的名稱，而 tenantID 是您的租用戶 GUID: <br>
`RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID>`



## <a name="see-also"></a>另請參閱

- [啟用 Azure Active Directory 的應用程式的 Proxy](active-directory-application-proxy-enable.md)
- [發佈應用程式使用您自己的網域名稱](active-directory-application-proxy-custom-domains.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [您正與應用程式 Proxy 的問題進行疑難排解](active-directory-application-proxy-troubleshoot.md)

最新消息和更新，請參閱[應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
