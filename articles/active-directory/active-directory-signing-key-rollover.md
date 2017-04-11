<properties
    pageTitle="Azure AD 中簽署金鑰變換 |Microsoft Azure"
    description="本文適用於 Azure Active Directory 討論的簽章的金鑰變換最佳作法"
    services="active-directory"
    documentationCenter=".net"
    authors="gsacavdm"
    manager="krassk"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="gsacavdm"/>

# <a name="signing-key-rollover-in-azure-active-directory"></a>登入 Azure Active Directory 中的金鑰變換

本主題探討必須知道的 Azure Active Directory (Azure AD) 中用來登入安全性權杖公用按鍵。 請務必記下這些按鍵變換定期，然後在緊急時，可能會立即復原。 使用 Azure AD 所有應用程式應該能夠以程式設計方式處理金鑰變換程序，或建立定期手動變換程序。 繼續閱讀以瞭解如何使用按鍵時，如何評估變換應用程式的影響，以及如何更新您的應用程式，或建立定期手動變換程序來處理金鑰變換，如有必要。

## <a name="overview-of-signing-keys-in-azure-ad"></a>中 Azure AD 簽章金鑰概觀

Azure AD 會使用內建在業界標準公開金鑰加密本身和使用的應用程式之間建立信任。 實際上，此作業的運作方式如下︰ Azure AD 使用公開及私密金鑰組組成的簽章金鑰。 當使用者登入使用 Azure AD 驗證的應用程式時，Azure AD 所建立的安全性權杖包含使用者的相關資訊。 Azure AD 傳送回應用程式之前，請使用其私密金鑰簽署此 token 加時。 若要驗證的權杖是有效且實際起始從 Azure AD，應用程式必須驗證使用公開金鑰公開 Azure AD 租用戶的[OpenID 連線探索文件](http://openid.net/specs/openid-connect-discovery-1_0.html)或 SAML/WS-Fed[同盟中繼資料文件](active-directory-federation-metadata.md)中所包含的權杖的簽章。

基於安全性理由，Azure AD 登入定期和緊急，若是索引鍵的資料無法復原立即。 Azure AD 與整合任何應用程式還需要準備處理金鑰變換事件，無論它可能會發生的頻率。 如果不是，且您的應用程式嘗試使用過期的鍵來確認簽章的權杖，就無法登入要求。

很多個有效的金鑰適用於 OpenID 連線探索文件和同盟中繼資料文件。 您的應用程式應該要準備好使用任何文件中所指定的索引鍵，因為可能推出復原一個索引鍵，另一個可能會及其取代項目，等等。

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>如何評估如果會影響您的應用程式，以及如何將您的意見

應用程式如何處理金鑰變換取決於應用程式，或使用何種身分識別通訊協定和文件庫的類型等的變數。 以下各節評估是否受到金鑰變換最常見的應用程式，並指導您如何更新應用程式支援換用自動或手動更新索引鍵。

* [存取資源的原生的用戶端應用程式](#nativeclient)
* [Web 應用程式 / Api 存取資源](#webclient)
* [Web 應用程式的 Api 保護資源，然後使用 Azure 應用程式服務建置](#appservices)
* [Web 應用程式 / Api 保護使用.NET OWIN OpenID 連線，WS Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 介軟體的資源](#owin)
* [Web 應用程式 / Api 保護使用.NET 核心 OpenID 連線或 JwtBearerAuthentication 介軟體的資源](#owincore)
* [Web 應用程式 / Api 保護使用 Node.js 密碼 azure ad 模組的資源](#passport)
* [Web 應用程式的 Api 保護資源，然後建立 Visual Studio 2015](#vs2015)
* [保護資源，並使用 Visual Studio 2013 建立的 web 應用程式](#vs2013)
* [Web Api 保護資源和 Visual Studio 2013 建立的](#vs2013_webapi)
* [保護資源，並使用 Visual Studio 2012 建立的 web 應用程式](#vs2012)
* [保護資源，並使用 Visual Studio 2010，使用 Windows 身分識別 Foundation 2008 o 建立的 web 應用程式](#vs2010)
* [Web 應用程式 / 保護資源使用任何其他文件庫，或以手動方式實作的任何支援的通訊協定的 Api](#other)

本指南為**不**適用於︰

* 從 Azure AD 應用程式庫 （包括自訂） 新增的應用程式會有個別的指導方針有關簽章金鑰。 [詳細資訊。](active-directory-sso-certs.md)
* 內部部署的應用程式發佈透過應用程式 proxy 不必擔心簽章金鑰。

### <a name="nativeclient"></a>存取資源的原生的用戶端應用程式

只在存取資源 （例如︰ 存在的應用程式 Microsoft Graph、 KeyVault、 Outlook API 其他 Microsoft Api） 通常只取得權杖，並將它傳遞給資源擁有者。 假設他們不要保護的任何資源，他們不會檢查權杖，因此不需要以確保正確登入。

原生的用戶端應用程式，無論桌面或行動裝置，屬於此類型，而且變換，因此不影響。

### <a name="webclient"></a>Web 應用程式 / Api 存取資源

只在存取資源 （例如︰ 存在的應用程式 Microsoft Graph、 KeyVault、 Outlook API 其他 Microsoft Api） 通常只取得權杖，並將它傳遞給資源擁有者。 假設他們不要保護的任何資源，他們不會檢查權杖，因此不需要以確保正確登入。

Web 應用程式和 web 使用應用程式專用流程的 Api (用戶端憑證 / 用戶端憑證) 屬於此類型，因此不受到變換。

### <a name="appservices"></a>Web 應用程式的 Api 保護資源，然後使用 Azure 應用程式服務建置

Azure 應用程式服務的驗證 / 授權 (EasyAuth) 功能已經有的自動處理金鑰變換必要的邏輯。

### <a name="owin"></a>Web 應用程式 / 保護資源使用.NET OWIN OpenID 連線，WS Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 介軟體的 Api

如果您的應用程式使用的.NET OWIN OpenID 連線，WS Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 介軟體，它既有必要的邏輯自動處理金鑰變換。

您可以確認您的應用程式會使用任何一種來尋找您的應用程式 Startup.cs 或 Startup.Auth.cs 中的下列程式碼片段的任何

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
    });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>Web 應用程式 / Api 保護使用.NET 核心 OpenID 連線或 JwtBearerAuthentication 介軟體的資源

如果您的應用程式使用.NET 核心 OWIN OpenID 連線或 JwtBearerAuthentication 介軟體，它既有必要的邏輯自動處理金鑰變換。

您可以確認您的應用程式會使用任何一種來尋找您的應用程式 Startup.cs 或 Startup.Auth.cs 中的下列程式碼片段的任何

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
    });
```

### <a name="passport"></a>Web 應用程式 / Api 保護使用 Node.js 密碼 azure ad 模組的資源

如果您的應用程式使用 Node.js 密碼 ad 模組，它既有必要的邏輯自動處理金鑰變換。

您可以請確認您的應用程式密碼 ad 搜尋您的應用程式 app.js 在下列程式碼片段

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>Web 應用程式的 Api 保護資源，然後使用 Visual Studio 2015 建立

如果在 Visual Studio 2015 中使用 web 應用程式範本建立應用程式時，從 [**變更驗證**] 功能表中選取**公司與學校帳戶**，則它既有自動處理金鑰變換必要的邏輯。 此內嵌在 OWIN OpenID 連線中介軟體中的邏輯擷取快取 OpenID 連線探索文件的鍵，並會定期重新整理。

如果您新增驗證您的方案以手動方式，您的應用程式可能沒有必要的金鑰變換邏輯。 您必須自行撰寫或依照[Web 應用程式 / Api 使用任何其他文件庫，或以手動方式實作的任何支援的通訊協定。](#other)。

### <a name="vs2013"></a>保護資源，並使用 Visual Studio 2013 建立的 web 應用程式

如果在 Visual Studio 2013 中使用 web 應用程式範本建立應用程式時，從 [**變更驗證**] 功能表中選取**組織帳戶，**則它既有自動處理金鑰變換必要的邏輯。 此邏輯會儲存您組織的唯一識別碼和簽署的重要資訊與專案相關聯的兩個資料庫資料表中。 您可以找到資料庫的連線字串中專案的 Web.config 檔案。

如果您新增驗證您的方案以手動方式，您的應用程式可能沒有必要的金鑰變換邏輯。 您必須自行撰寫或依照[Web 應用程式 / Api 使用任何其他文件庫，或以手動方式實作的任何支援的通訊協定。](#other)。

下列步驟可協助您確認邏輯應用程式中正常運作。

1. 在 Visual Studio 2013 中，開啟方案，然後按一下**伺服器總管**] 索引標籤上 [右] 視窗。
2. 展開 [**資料連線**、 **DefaultConnection**，及**表格**]。 找出**IssuingAuthorityKeys**表格，以滑鼠右鍵按一下，再按一下 [**顯示資料表的資料**。
3. 在**IssuingAuthorityKeys**資料表中，會有至少一個資料列，其會對應鍵指紋值。 刪除表格中的任何列。
4. 以滑鼠右鍵按一下 [**租用戶**] 資料表，然後按一下 [**顯示資料表的資料**。
5. 在**租用戶**資料表中，會對應到唯一目錄租用戶識別碼的至少有一個資料列。 刪除表格中的任何列。 如果您沒有刪除**租用戶**表格和**IssuingAuthorityKeys**表格中的資料列，您會收到錯誤，在執行階段。
6. 建立並執行應用程式。 您已登入您的帳戶後，您可以停止應用程式。
7. 返回**伺服器總管]** ，然後查看 [ **IssuingAuthorityKeys**和**租用戶**的表格中的值。 您會發現，他們有已自動重新建立同盟中繼資料文件中的適當的資訊。

### <a name="vs2013"></a>Web Api 保護資源和 Visual Studio 2013 建立的

如果您使用 Web API 範本，Visual Studio 2013 中建立的 web API 應用程式，然後從 [**變更驗證**] 功能表中選取 [**組織帳戶**，您已經擁有必要的邏輯應用程式中。

如果您手動設定驗證，請遵循下列指示來瞭解如何設定自動更新其重要資訊您網站的 API。

下列程式碼片段示範如何從同盟中繼資料文件中，取得最新的索引鍵，然後使用[JWT Token 處理常式](https://msdn.microsoft.com/library/dn205065.aspx)驗證的權杖。 程式碼片段假設您會使用您自己的快取機制保存鍵以驗證從 Azure AD，未來的權杖不管是在資料庫、 設定檔，或其他位置。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>保護資源，並使用 Visual Studio 2012 建立的 web 應用程式

如果 Visual Studio 2012 上建立您的應用程式，您可能使用的身分識別和存取工具來設定您的應用程式。 也可能是您使用的[驗證簽發者名稱登錄 (VINR)](https://msdn.microsoft.com/library/dn205067.aspx)。 VINR 負責維護受信任的身分識別提供者 (Azure AD) 的相關資訊以及用來驗證其發出的權杖的金鑰。 VINR 也可輕鬆自動更新的資訊儲存在 Web.config 檔案下載最新與您的目錄，相關聯的同盟中繼資料文件檢查是否是最新的最新的文件，請設定和更新以使用新的金鑰所需的應用程式。

如果您建立您的應用程式使用任何的程式碼範例或由 Microsoft 提供的逐步解說文件，在專案中已包含金鑰變換邏輯。 您會發現您專案中的已經有以下的程式碼。 如果您的應用程式已經沒有這個邏輯，遵循下列步驟，將它新增並確認其運作正常。

1. 在**方案總管**] 中，新增適當的專案的**System.IdentityModel**組件的參考。
2. 開啟**Global.asax.cs**檔案，並新增下列使用指示詞︰
```
using System.Configuration;
using System.IdentityModel.Tokens;
```
3. 新增下列方法**Global.asax.cs**檔案︰
```
protected void RefreshValidationSettings()
{
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
}
```
4. 叫用**RefreshValidationSettings()**方法中**Global.asax.cs** **Application_Start()**方法如下圖所示︰
```
protected void Application_Start()
{
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
}
```

一旦您遵循這些步驟，應用程式的 Web.config 就會更新同盟中繼資料文件，包括最新的機碼中的最新的資訊。 每次應用程式集區回收 IIS; 中時，會發生此更新依預設 IIS 會設定為每隔 29 小時資源應用程式。

請遵循下列步驟來驗證金鑰變換邏輯正常運作。

1. 您已驗證您的應用程式會使用上述的程式碼之後，開啟**Web.config**檔案，然後瀏覽至**<issuerNameRegistry>**區塊，特別尋找下列幾行︰
```
<issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
```
2. 在 [**<add thumbprint=””>**設定，來變更指紋值取代以不同的任何字元。 儲存**Web.config**檔案。

3. 建立應用程式，然後執行。 您可以完成登入程序，如果您的應用程式順利更新索引鍵從您的目錄同盟中繼資料文件下載所需的資訊。 如果您有登入方面的問題，請確定您的應用程式中的變更正確閱讀[新增登入至您的 Web 應用程式使用 Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect)主題或下載，透過檢查下列程式碼範例︰[的 Azure Active Directory 多重租用戶雲端應用程式](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)。


### <a name="vs2010"></a>保護資源，並使用 Visual Studio 2008 或 2010年建立的 web 應用程式和 Windows 身分識別 Foundation (WIF) v1.0 的.NET 3.5

如果您建置 WIF v1.0 上的應用程式，但沒有提供的機制自動重新整理您的應用程式設定以使用新的金鑰。

- *最簡單的方法*使用 FedUtil 工具包含在 WIF sdk，您可以以擷取最新的中繼資料文件，並更新您的設定。
- 更新您的應用程式.NET 4.5，其中包括最新版的 WIF 位於系統命名空間。 [驗證發行者名稱登錄 (VINR)](https://msdn.microsoft.com/library/dn205067.aspx)然後可用來執行的應用程式的設定自動更新。
- 執行手動變換依照本指南文件結尾處的指示進行。

若要使用 FedUtil 更新您的設定指示︰

1. 確認您有 WIF v1.0 SDK 開發電腦上安裝的 Visual Studio 2008 或 2010年。 您可以[從這裡開始下載](https://www.microsoft.com/en-us/download/details.aspx?id=4451)如果您未安裝它。
2. 在 Visual Studio 中，開啟方案，然後以滑鼠右鍵按一下適用的專案，並選取**更新同盟中繼資料**。 如果無法使用這個選項，FedUtil 及/或 WIF v1.0 SDK 尚未安裝。
3. 從提示時，選取 [**更新**] 以開始更新您的同盟中繼資料]。 如果您有權存取裝載應用程式的伺服器環境時，您可以選擇性地使用 FedUtil 的[自動更新的排程的中繼資料](https://msdn.microsoft.com/library/ee517272.aspx)。
4. 按一下 [**完成**] 以完成更新程序。

### <a name="other"></a>Web 應用程式 / 保護資源使用任何其他文件庫，或以手動方式實作的任何支援的通訊協定的 Api

如果您使用的其他一些文件庫，或是手動實作的任何支援的通訊協定，您會需要檢閱的文件庫或您的實作，以確保索引鍵的正在擷取 OpenID 連線探索文件或同盟中繼資料文件。 若要檢查的其中一個方法是在中執行搜尋您的程式碼或文件庫的程式碼出任何通話 OpenID 探索文件或同盟中繼資料文件。

如果他們主要位置儲存或應用程式中的硬式編碼，則可以手動擷取索引鍵和更新，會相應地執行手動變換依照本指南文件結尾處的指示進行。 若要避免未來中斷本文中**我們強烈建議您增強支援自動換用您的應用程式**使用任何方法大綱及負荷如果 Azure AD 增加時，才可變換頻率，或具有緊急-頻變換。

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>如何測試判斷是否會影響應用程式

無論您的應用程式支援自動金鑰變換下載指令碼中的指示執行，您可以驗證[此 GitHub 儲存機制。](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>如果您的應用程式不支援自動變換執行手動換用的方法

如果您的應用程式**不**支援自動換用，您必須建立定期監視 Azure AD 簽章金鑰，並會相應地執行手動換用的程序。 [此 GitHub 存放庫](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)包含指令碼及如何執行此動作的指示進行。
