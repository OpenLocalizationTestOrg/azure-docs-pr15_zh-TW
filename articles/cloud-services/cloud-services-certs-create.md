<properties 
    pageTitle="雲端服務與管理憑證 |Microsoft Azure" 
    description="瞭解如何建立和使用 Microsoft Azure 中的憑證" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="cloud-services" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016"
    ms.author="adegeo"/>

# <a name="certificates-overview-for-azure-cloud-services"></a>Azure 雲端服務的憑證概觀
雲端服務 （[服務的憑證](#what-are-service-certificates)），以及管理 API （[管理憑證](#what-are-management-certificates)時使用 Azure 傳統入口網站和不 ARM） 驗證 Azure 中使用憑證。 本主題提供這兩種憑證類型，概述如何[建立](#create)並[部署](#deploy)至 Azure。

有 x.509 v3 憑證 Azure 中使用的憑證，且可以由另一個受信任的憑證簽署或可自我簽署。 自我簽署的憑證簽署自己建立者，因此，不會受預設]。 大部分的瀏覽器可以略過。 自我簽署的憑證應該只用於自己時開發及測試您的雲端服務。 

私人或公開金鑰，可以包含 Azure 所使用的憑證。 憑證必須提供更明確的方式將其識別指紋。 此指紋 Azure 的[設定檔](cloud-services-configure-ssl-certificate.md)中用於識別應該使用雲端服務的憑證。 

## <a name="what-are-service-certificates"></a>什麼是服務的憑證？
服務的憑證附加至雲端服務，並啟用安全通訊與服務。 例如，如果您部署網頁角色，您會想要提供可以驗證公開的 HTTPS 端點的憑證。 自動執行您的角色的執行個體的虛擬機器部署服務的憑證，在您的服務定義中定義。 

Azure 傳統的入口網站，請使用 [Azure 傳統入口網站，或使用服務管理 API，您可以上傳服務的憑證。 服務的憑證會在特定的雲端服務相關聯，並指派給服務定義檔案中的部署。

服務的憑證可從您的服務，分別管理，且可能以不同的個人管理。 例如，開發人員可能上傳的參照憑證的 IT 管理員先前已上傳至 Azure 服務套件。 IT 管理員可以管理並更新變更服務的設定，並不需要上傳新的服務套件的憑證。 這可能是因為時服務設定檔中指定的憑證指紋服務定義檔案中指定憑證其存放名稱和位置的邏輯名稱。 若要更新的憑證，才需要上傳新的憑證，並變更指紋值服務設定檔中。

## <a name="what-are-management-certificates"></a>什麼是管理憑證？
管理憑證可讓您 Azure 傳統所提供的服務管理 API 驗證方法。 多個程式和工具 （例如 Visual Studio 或 Azure SDK） 會使用這些憑證來自動化設定及部署各種 Azure 服務。 這些不是真正相關雲端服務。 

>[AZURE.WARNING] 請小心 ！ 這些類型的憑證允許驗證，來管理的訂閱相關的任何人。 

### <a name="limitations"></a>限制
有 100 管理憑證，每個訂閱的限制。 也有特定的服務系統管理員的使用者識別碼底下的所有訂閱的 100 管理憑證的限制 如果要將 100 管理憑證新增的帳戶管理員的使用者識別碼已使用需要更多的憑證，您可以新增的共同管理員，以新增額外的憑證。 

前 100 個以上的憑證，請參閱是否您可以重複使用現有的憑證。 使用共同管理員加入您的憑證管理程序可能不必要的複雜性。


<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>建立新的自我簽署的憑證
您可以使用任何工具，可供建立自我簽署的憑證，前提是他們依循這些設定︰

* X.509 憑證。
* 包含私人的金鑰。
* 建立金鑰交換 （.pfx 檔）。
* 主體名稱必須符合用來存取雲端服務的網域。 
    > 您無法取得 SSL 憑證 cloudapp.net （或任何相關的 Azure） 的網域。憑證的主體名稱必須符合用來存取您的應用程式的自訂網域名稱。 例如， **contoso.net**、 **contoso.cloudapp.net**。
* 2048 位元加密的最小值。
* **僅服務的憑證**︰ 用戶端憑證必須位於的*個人*憑證存放區。

有兩種簡單方式建立的憑證，在 Windows 上，使用`makecert.exe`公用程式或 IIS。

### <a name="makecertexe"></a>Makecert.exe

此公用程式已被取代，無法在此說明。 請參閱[MSDN 上的本文](https://msdn.microsoft.com/library/windows/desktop/aa386968)，如需詳細資訊。

### <a name="powershell"></a>PowerShell

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

>[AZURE.NOTE] 如果您想要使用的憑證，而不是網域 IP 位址，請-DnsName 參數中使用的 IP 位址。


如果您想要使用這個[憑證管理入口網站](../azure-api-management-certs.md)，則會匯出為**.cer**檔案︰

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)

有許多在網際網路上的頁面中涵蓋如何進行此動作 iis。 [以下](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html)是很好我找到我想也會提供說明。 

### <a name="java"></a>Java
您可以使用 Java 建立[憑證](../app-service-web/java-create-azure-website-using-java-sdk.md#create-a-certificate)。

### <a name="linux"></a>Linux
[本文](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md)將說明如何使用 SSH 建立憑證。

## <a name="next-steps"></a>後續步驟

[上傳您服務的憑證 Azure 傳統入口網站](cloud-services-configure-ssl-certificate.md)（或[Azure 入口網站](cloud-services-configure-ssl-certificate-portal.md)）。

Azure 傳統入口網站上, 傳[管理 API 憑證](../azure-api-management-certs.md)。

>[AZURE.NOTE] Azure 入口網站存取 API 未使用的憑證管理，但會使用使用者帳戶。
