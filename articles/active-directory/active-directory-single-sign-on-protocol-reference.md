<properties
    pageTitle="Azure 的單一登入 [SAML 通訊協定 |Microsoft Azure"
    description="本文將說明 Azure Active Directory 中的單一登入 SAML 通訊協定"
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

# <a name="single-sign-on-saml-protocol"></a>單一登入 SAML 通訊協定

本文涵蓋 SAML 2.0 驗證的邀請和回覆 Azure Active Directory (Azure AD) 支援的單一登入。

通訊協定下圖說明在單一登入順序。 雲端服務 （服務提供者） 使用 HTTP 重新導向繫結傳遞`AuthnRequest`Azure AD （驗證要求） 項目 （身分識別提供者）。 Azure AD 然後使用 HTTP 張貼繫結至張貼`Response`雲端服務的項目。

![單一登入工作流程](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

若要要求使用者驗證，雲端服務傳送`AuthnRequest`Azure AD 項目。 樣本 SAML 2.0`AuthnRequest`可能看起來像這樣︰

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```


| 參數 | | 描述 |
| ----------------------- | ------------------------------- | --------------- |
| 識別碼 | 所需 | Azure AD 使用此屬性，填入`InResponseTo`傳回回應的屬性。 識別碼開頭不是數字，所以常見的策略是在前面加上的字串，例如 「 識別碼 」 GUID 的字串表示。 例如，`id6c1c178c166d486687be4aaf5e482730`是有效的識別碼。 |
| 版本 | 所需 | 應該是**2.0**。|
| IssueInstant | 所需 | 這是 DateTime 字串 UTC 值與[來回格式 (「 o 」)](https://msdn.microsoft.com/library/az4se3k1.aspx)。 Azure AD 預期這種 DateTime 值，但不會評估或使用的值。 |
| AssertionConsumerServiceUrl | 選擇性 | 如果所提供，這必須符合`RedirectUri`Azure AD 中的雲端服務。 |
| ForceAuthn | 選擇性 | 如有提供應該是 false。 其他任何值會導致錯誤。|
| IsPassive | 選擇性 | 如有提供應該是 false。 其他任何值會導致錯誤。 |  

所有其他`AuthnRequest`屬性，例如同意、 目的地、 AssertionConsumerServiceIndex、 AttributeConsumerServiceIndex 和 ProviderName 會**忽略**。

Azure AD 也會忽略`Conditions`中的項目`AuthnRequest`。

### <a name="issuer"></a>發行者

`Issuer`中的項目`AuthnRequest`必須確實符合**ServicePrincipalNames**雲端服務中的其中一個 Azure AD 中。 一般而言，這是設定**應用程式識別碼 URI**應用程式註冊期間所指定。

範例 SAML 摘錄包含`Issuer`項目看起來像這樣︰

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

這個項目要求在回應中的特定名稱編號格式，並為選用步驟中`AuthnRequest`傳送至 Azure AD 的項目。

樣本`NameIdPolicy`項目看起來像這樣︰

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

如果`NameIDPolicy`會提供您可以包含其選擇性`Format`屬性。 `Format`屬性可能只有一個下列的值。其他任何值會產生錯誤。

-  `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory 問題 NameID 宣告為成對的識別碼。
- `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory 中發生的問題 NameID 宣告電子郵件地址的格式。
- `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`︰ 此值會允許選取宣告格式的 Azure Active Directory。 Azure Active Directory 問題 NameID 為成對的識別碼。

不包含`SPNameQualifer`屬性。 Azure AD 會忽略`AllowCreate`屬性。

### <a name="requestauthncontext"></a>RequestAuthnContext

`RequestedAuthnContext`項目會指定所要的驗證方法。 它是選擇性的`AuthnRequest`傳送至 Azure AD 的項目。 Azure AD 支援只有一個`AuthnContextClassRef`值︰ `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`。

### <a name="scoping"></a>範圍設定

`Scoping`是選擇性的項目，包括身分識別提供者的清單，`AuthnRequest`傳送至 Azure AD 的項目。

如果所提供，請勿將`ProxyCount`屬性，`IDPListOption`或`RequesterID`項目，請為不受支援。

### <a name="signature"></a>簽章

不包含`Signature`中的項目`AuthnRequest`元素，為不支援 Azure AD 簽署驗證要求。

### <a name="subject"></a>主旨

Azure AD 會忽略`Subject`的項目`AuthnRequest`項目。

## <a name="response"></a>回應

當要求登入順利完成時，Azure AD 文章的回應雲端服務。 成功的登入嘗試的範例回應看起來像這樣︰

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>回應

`Response`元素包含結果的授權要求。 Azure AD 集`ID`，`Version`和`IssueInstant`值在`Response`項目。 它也會將下列屬性︰

- `Destination`︰ 登入順利完成，這會設定為`RedirectUri`的服務提供者 （雲端服務）。
- `InResponseTo`︰ 這設定為`ID`屬性`AuthnRequest`發起的租用戶回應的項目。

### <a name="issuer"></a>發行者

Azure AD 集`Issuer`項目`https://login.microsoftonline.com/<TenantIDGUID>/`位置<TenantIDGUID>Azure AD 租用戶的租用戶 id。

例如，發行者的項目為範例回應起來可能像這樣︰

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>狀態

`Status`元素傳達的成功或失敗的登入。 包括`StatusCode`項目，其中包含的程式碼或一組的巢狀的代碼，用來代表要求的狀態。 也包含`StatusMessage`項目，其中包含登入程序時所產生的自訂錯誤訊息。

<!-- TODO: Add a authentication protocol error reference -->

以下是成功的登入嘗試的 SAML 回應。

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>判斷提示

除了`ID`，`IssueInstant`和`Version`，Azure AD 設定下列項目中`Assertion`回應的項目。

#### <a name="issuer"></a>發行者

這設定為`https://sts.windows.net/<TenantIDGUID>/`位置<TenantIDGUID>Azure AD 租用戶的租用戶 id。

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>簽章

Azure AD 簽署，判斷提示回應成功的登入。 `Signature`項目會包含可用來驗證的來源，以判斷提示的完整性雲端服務的數位簽章。

若要產生此數位簽章，Azure AD 使用中的簽章索引鍵`IDPSSODescriptor`其中繼資料文件項目。

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>主旨

這會指定是主詞，判斷提示中的陳述式的本金。 包含`NameID`項目，代表已驗證的使用者。 `NameID`值是目標的識別碼，只導向至權杖的對象的服務提供者。 它會持續-可以撤銷，但不是會指派。 這也是不透明的並不顯示使用者的相關的任何項目，且無法用於做為識別項屬性的查詢。

`Method`屬性`SubjectConfirmation`項目會永遠設為`urn:oasis:names:tc:SAML:2.0:cm:bearer`。

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>條件

這個項目指定條件的定義可接受的使用 SAML 判斷提示。

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore`和`NotOnOrAfter`屬性指定期間判斷提示是有效的時間間隔。

- 值的`NotBefore`屬性等於，或稍微 （小於第二） 晚的值`IssueInstant`屬性`Assertion`項目。 Azure AD 並未處理任何時間與之間的差異本身雲端服務 （服務提供者），這次不會加入任何緩衝。
- 值的`NotOnOrAfter`屬性是 [70 分鐘晚的值`NotBefore`屬性。

#### <a name="audience"></a>對象

此頁面包含 URI，識別預定的對象。 Azure AD 設定此項目的值的值`Issuer`的項目`AuthnRequest`的啟動登入。 評估`Audience`值，使用的值，`App ID URI`的指定應用程式註冊期間。

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

例如`Issuer`值，`Audience`值必須完全符合其中一項服務主體名稱，代表 Azure AD 雲端服務。 不過，如果的值`Issuer`項目不 URI 值，`Audience`在回應中的值是`Issuer`值前面加上`spn:`。

#### <a name="attributestatement"></a>AttributeStatement

此頁面包含主旨或使用者相關的宣告。 下列摘錄包含樣本`AttributeStatement`項目。 省略符號會指出此項目，可包含多個屬性及屬性值。

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```     

- **名稱宣告**︰ 的值`Name`屬性 (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) 是已驗證的使用者的使用者主要名稱，例如`testuser@managedtenant.com`。
- **ObjectIdentifier 宣告**︰ 的值`ObjectIdentifier`屬性 (`http://schemas.microsoft.com/identity/claims/objectidentifier`) 是`ObjectId`目錄物件，代表 Azure AD 經過驗證的使用者。 `ObjectId`不變，全域唯一的並重複使用安全經過驗證的使用者識別碼。

#### <a name="authnstatement"></a>AuthnStatement

這個項目判斷提示判斷提示主旨已驗證的特定所代表的意義在特定的時間。

- `AuthnInstant` Azure AD 屬性指定驗證使用者的時間。
- `AuthnContext`項目會指定驗證使用者使用的驗證內容。

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
