<properties
    pageTitle="使用 Web 應用程式從 Azure 金鑰保存庫 |Microsoft Azure"
    description="使用此教學課程，協助您瞭解如何使用 Azure 金鑰保存庫從 web 應用程式。"
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>使用 Web 應用程式從 Azure 金鑰保存庫 #

## <a name="introduction"></a>簡介  
使用此教學課程，協助您瞭解如何使用 Azure 金鑰保存庫從 Azure 中的 web 應用程式。 它逐步引導您進行存取私人從 Azure 金鑰保存庫，這樣就可以使用 web 應用程式中的程序。

**估計完成時間︰** 15 分鐘


Azure 鍵保存庫相關的概觀資訊，請參閱[什麼是 Azure 金鑰保存庫？](key-vault-whatis.md)

## <a name="prerequisites"></a>必要條件

若要完成此教學課程中，您必須具備下列項目︰

- Azure 鍵保存庫中的密碼 URI
- 用戶端識別碼] 和 [web 應用程式的用戶端密碼登錄的 Azure Active Directory 有權存取您的金鑰保存庫
- Web 應用程式。 我們會顯示 ASP.NET MVC 應用程式部署 Azure 為 Web App 中的步驟。

> [AZURE.NOTE]  請務必假設您已完成所列的步驟[快速入門 Azure 金鑰保存庫](key-vault-get-started.md)此教學課程中，讓您有私人的用戶端識別碼和用戶端私人 URI web 應用程式。

存取鍵保存庫 web 應用程式是已註冊 Azure Active Directory 中，具有存取至您的金鑰保存庫。 如果沒有大小寫，請回到登錄開始教學課程中的應用程式，然後重複步驟所列。

本教學課程為設計 web 開發人員瞭解 Azure 上建立 web 應用程式的基本概念。 如需有關 Azure Web 應用程式的詳細資訊，請參閱[Web Apps 概觀](../app-service-web/app-service-web-overview.md)。



## <a id="packages"></a>新增 Nuget 套件 ##
有兩個 web 應用程式必須已安裝的套件。

- Active Directory 驗證庫-包含與 Azure Active Directory 互動及管理使用者身分識別的方法
- Azure 的金鑰保存庫文件庫-包含與 Azure 金鑰保存庫互動的方法


兩個這些套件可以使用套件管理員主控台使用安裝套件] 命令會安裝。

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>修改 Web.Config ##
有三個需要加入至 web.config 檔，如下所示的應用程式設定。

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


如果您不主控您的應用程式為 Azure Web 應用程式，您應該 web.config 新增 ClientId、 用戶端密碼和密碼 URI 的實際值。 否則保留這些空值，因為我們將會在 Azure 入口網站中的其他安全性層級新增實際的值。


## <a id="gettoken"></a>新增可存取權杖的方法 ##
若要使用金鑰保存庫 API，您會需要存取權杖。 索引鍵保存庫用戶端控點的來電至] 鍵保存庫 API，但您需要提供取得存取權杖的函數。  

以下是要存取權杖從 Azure Active Directory 的程式碼。 將此程式碼可以隨時隨地應用程式中。 我要加入 Utils 或 EncryptionHelper 類別。  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> 使用的用戶端識別碼和用戶端密碼是最簡單的方法驗證 Azure AD 應用程式。 並使用 web 應用程式中允許責任區隔和更多的控制您金鑰管理。 但它會依賴將用戶端私人放在您設定的設定可針對某些為風險為放在您設定的設定中所要保護的密碼。 請參閱下方如何使用的用戶端識別碼和憑證，而不是用戶端識別碼] 和 [用戶端密碼驗證 Azure AD 應用程式上的討論。



## <a id="appstart"></a>擷取上啟動應用程式的密碼 ##
現在，我們需要致電鍵保存庫 API，並擷取機密的程式碼。 下列程式碼放任何一處，只要稱為前必須先使用它。 我必須中啟動應用程式中的事件 Global.asax 放將此程式碼，使其執行一次在啟動，並讓私人使用應用程式。

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>（選擇性） Azure 入口網站中新增應用程式設定 ##
如果您有 Azure Web 應用程式您現在可以加入的和 AppSettings Azure 入口網站中實際的值。 執行此動作，不會在 web.config 實際的值，但受到入口網站，您必須個別存取權控制功能。 在您 web.config 中所輸入的值會取代下列的值。 請確定名稱都相同。

![Azure 入口網站中顯示的應用程式設定][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>驗證，而不是用戶端私人的憑證
驗證 Azure AD 應用程式的另一個方法是使用用戶端識別碼和憑證，而非的用戶端識別碼和用戶端密碼。 以下是使用的憑證 Azure Web 應用程式中的步驟︰

1. 取得或建立憑證
2. 建立憑證 Azure AD 應用程式之間的關聯
3. 將程式碼新增至您的 Web 應用程式使用的憑證
4. 將憑證新增至您的 Web 應用程式


**取得或建立憑證**我們的目的，我們就能測試憑證。 以下是一些您可以使用 [在開發人員命令提示字元來建立憑證的命令。 將目錄變更您想要建立的憑證檔案的位置。

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

請記下的結束日期和.pfx 的密碼 (在此範例中︰ 07/31/2016年和 test123)。 您將需要其下方。

如需有關如何建立測試憑證的詳細資訊，請參閱[如何︰ 建立您自己測試憑證](https://msdn.microsoft.com/library/ff699202.aspx)


**建立關聯的 Azure AD 應用程式的憑證**您已經有憑證，必須將其關聯 Azure AD 應用程式。 但 Azure 管理入口網站不支援此立即。 不過，您必須使用 Powershell。 以下是您需要執行的命令︰

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

執行下列命令之後，您可以看到 Azure AD 中的應用程式。 如果您沒有看到 [第一個，搜尋 「 我的公司所擁有的應用程式 」 應用程式，而不是 」 應用程式我的公司使用 」。

若要進一步瞭解 Azure AD 應用程式物件及 ServicePrincipal 物件，請參閱[應用程式與服務主要物件](../active-directory/active-directory-application-objects.md)



**新增您的 Web 應用程式使用的憑證的程式碼**現在我們會將程式碼新增至您的 Web 應用程式存取的憑證，並使用它來進行驗證。

第一次沒有存取憑證的程式碼。

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


請注意，StoreLocation，而不是 LocalMachine CurrentUser。 然後，我們會提供 'false' 尋找方法因為我們使用測試憑證。


下一步是使用 CertificateHelper 並建立所需的驗證 ClientAssertionCertificate 的程式碼。

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


以下是新的程式碼，以取得存取權杖。 這會取代上述 GetToken 方法。 我有提供，方便使用不同的名稱。

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

我必須放這段程式碼到我的 Web App 專案 Utils 類別中，以便於使用。

上一個程式碼變更位於 Application_Start 方法。 首先，我們需要呼叫載入 ClientAssertionCertificate GetCert() 方法。 然後我們變更時建立新的 KeyVaultClient 我們提供的回撥方法。 請注意，這會取代我們上方的程式碼。

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**新增至您的 Web 應用程式，透過 Azure 入口網站的憑證**將憑證新增至您的 Web 應用程式是簡單的兩個步驟。 首先，請移至 [Azure 入口網站，然後瀏覽至您的 Web 應用程式。 在 Web 應用程式設定刀，按一下 「 自訂網域和 SSL 」 的項目。 隨後便會開啟您刀會無法上傳您建立上述 KVWebApp.pfx 的憑證，請確定您記得 pfx 的密碼。

![將憑證新增至 Azure 入口網站中的 Web 應用程式][2]


您需要執行的最後一個項目會新增至您的名稱網站的 Web 應用程式的應用程式設定\_載入\_憑證和值 *。 這樣就能確定會載入的憑證。 如果您想要載入您上傳的憑證，您可以輸入其憑證碼逗點分隔清單。

若要進一步瞭解將憑證新增至 Web 應用程式，請參閱[Azure 網站應用程式中使用的憑證](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**新增要為私人的金鑰保存庫的憑證**而不是直接 Web 應用程式服務傳您的憑證，您可以將其儲存為私人的金鑰保存庫中，並將其部署從該位置。 這是執行的程序的下列部落格文章，[透過金鑰保存庫部署 Azure Web 應用程式認證](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>後續步驟 ##


程式設計參照，請參閱[Azure 金鑰保存庫 C# 用戶端 API 參照](https://msdn.microsoft.com/library/azure/dn903628.aspx)。


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
