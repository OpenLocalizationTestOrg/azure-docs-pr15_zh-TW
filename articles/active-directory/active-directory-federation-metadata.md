<properties
    pageTitle="Azure AD 同盟中繼資料 |Microsoft Azure"
    description="本文將說明的 Azure Active Directory 發佈接受 Azure Active Directory 權杖的服務的同盟中繼資料文件。"
    services="active-directory"
    documentationCenter=".net"
    authors="priyamohanram"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="priyamo"/>


# <a name="federation-metadata"></a>同盟中繼資料

Azure Active Directory (Azure AD) 發佈同盟中繼資料文件的設定為接受 Azure AD 問題安全性權杖的服務。 同盟中繼資料文件格式是由[Web 服務同盟語言 （WS 同盟） 1.2 版](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)，以擴充[OASIS 安全性判斷提示標記語言 (SAML) v2.0 中繼資料](http://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)所述。

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>租用戶專屬和租用戶無關的中繼資料端點

Azure AD 發佈租用戶專屬和租用戶無關的結束點。

租用戶專屬端點專為特定的租用戶。 租用戶專屬同盟中繼資料包含租用戶，包括租用戶專屬發行者及結束點資訊的相關資訊。 限制存取至單一的租用戶的應用程式使用特定的租用戶的結束點。

租用戶獨立端點提供通用到所有 Azure AD 租用戶的資訊。 這項資訊套用*login.microsoftonline.com*主控的租用戶，而租用戶跨共用。 租用戶獨立端點建議多租用戶的應用程式，因為他們不是與任何特定的租用戶相關聯。

## <a name="federation-metadata-endpoints"></a>同盟中繼資料端點

Azure AD 發佈同盟中繼資料`https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`。

為**特定的租用戶的結束點**，`TenantDomainName`可以是下列類型的其中一個︰

- 已註冊的網域名稱的 Azure AD 租用戶，例如︰ `contoso.onmicrosoft.com`。
- 不變租用戶的網域，例如`72f988bf-86f1-41af-91ab-2d7cd011db45`。

**獨立的租用戶的結束點**，`TenantDomainName`是`common`。 這份文件列出所有 Azure AD 租用戶裝載在 login.microsoftonline.com 常見的僅限同盟中繼資料項目。

可能的租用戶專屬端點，例如`https:// login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`。 租用戶獨立端點是[https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml)。 您可以在瀏覽器中，輸入此 URL 檢視同盟中繼資料文件。

## <a name="contents-of-federation-metadata"></a>同盟中繼資料的內容

下一節會提供取用權杖 Azure AD 發出的服務所需的資訊。

### <a name="entity-id"></a>實體識別碼

`EntityDescriptor`項目包含`EntityID`屬性。 值的`EntityID`屬性代表簽發者，也就是安全性權杖服務 (STS) 發出權杖。 請務必驗證發行者，當您收到權杖。

下列中繼資料顯示範例租用戶專屬`EntityDescriptor`的項目`EntityID`項目。

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
取代您的租用戶識別碼建立租用戶專屬的租用戶獨立端點的租用戶識別碼`EntityID`值。 所產生的值會 token 簽發者相同。 策略允許多租用戶應用程式驗證指定的租用戶的發行者。

下列中繼資料顯示範例租用戶獨立`EntityID`項目。 請注意，`{tenant}`常值，而不版面配置區。

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>權杖簽署憑證

當服務接收發出 Azure AD 租用戶的權杖時，必須驗證的權杖簽章，發佈同盟中繼資料文件中以簽署金鑰。 同盟中繼資料包含公用部分的租用戶用於權杖簽署憑證。 憑證原始位元組會出現在`KeyDescriptor`項目。 簽章憑證權杖是進行簽章時才有效的值`use`屬性是`signing`。

Azure AD 發佈同盟中繼資料文件可以有多個簽章金鑰的詳細資訊，例如 Azure AD 更新憑證的準備時。 當同盟中繼資料文件包含一個以上的憑證時，已驗證的權杖服務應該文件中支援的憑證。

下列中繼資料中顯示範例`KeyDescriptor`簽署的金鑰的項目。

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor`項目會顯示在兩個位置同盟中繼資料文件。在 [WS 同盟特定區段和 SAML 特定] 區段。 這兩個區段的憑證都相同。

[WS 同盟特定] 區段中 WS 同盟中繼資料讀取程式會讀取從憑證`RoleDescriptor`的項目`SecurityTokenServiceType`類型。

下列中繼資料中顯示範例`RoleDescriptor`項目。

```
<RoleDescriptor xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:fed="http://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="http://docs.oasis-open.org/wsfed/federation/200706">
```

在 SAML 專屬區段中，WS 同盟中繼資料讀取程式會讀取從憑證`IDPSSODescriptor`項目。

下列中繼資料中顯示範例`IDPSSODescriptor`項目。

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
沒有差異的租用戶專屬和租用戶獨立的憑證的格式。

### <a name="ws-federation-endpoint-url"></a>WS 同盟端點 URL

同盟中繼資料包含 Azure AD 使用單一登入和單一教具借出 WS 同盟通訊協定的 URL。 此端點會出現在`PassiveRequestorEndpoint`項目。

下列中繼資料中顯示範例`PassiveRequestorEndpoint`租用戶專屬端點的項目。

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
租用戶獨立端點，WS 同盟 URL 會出現在 WS 同盟端點，如下列範例所示。

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML 通訊協定端點 URL

同盟中繼資料包含 Azure AD 使用單一登入和單一教具借出 SAML 2.0 通訊協定的 URL。 這些端點會出現在`IDPSSODescriptor`項目。

登入及教具借出 Url 會出現在`SingleSignOnService`和`SingleLogoutService`項目。

下列中繼資料中顯示範例`PassiveResistorEndpoint`租用戶專屬端點。

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

同樣的常見 SAML 2.0 通訊協定端點的結束點發佈在租用戶獨立同盟中繼資料，如下列範例所示。

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
