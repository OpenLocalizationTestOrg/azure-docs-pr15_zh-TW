<properties
    pageTitle="Azure 單一登出 SAML 通訊協定 |Microsoft Azure"
    description="本文將說明 Azure Active Directory 中的單一 Sign-Out SAML 通訊協定"
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


# <a name="single-sign-out-saml-protocol"></a>單一教具借出 SAML 通訊協定

Azure Active Directory (Azure AD) 支援 SAML 2.0 網頁瀏覽器單一教具借出設定檔。 針對單一教具借出正常運作，Azure AD 必須註冊中繼資料 URL 應用程式註冊期間。 Azure AD 取得中繼資料登出 URL] 與 [雲端服務的簽章索引鍵。 Azure AD 使用簽署金鑰來驗證內送的 LogoutRequest 的簽章，並且已登出之後，重新導向使用者使用 LogoutURL。

如果雲端服務不支援的中繼資料端點，則登錄應用程式之後，開發人員必須連絡 Microsoft 支援服務，提供登出 URL 和簽署金鑰。

此圖顯示 Azure AD 單一教具借出程序的工作流程。

![工作流程的單一登出](media/active-directory-single-sign-out-protocol-reference/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest

雲端服務給`LogoutRequest`Azure AD 訊息，表示已經結束工作階段。 下列摘錄顯示範例`LogoutRequest`項目。

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest

`LogoutRequest`項目傳送至 Azure AD 需要下列屬性︰

- `ID`︰ 這會識別教具借出要求。 值的`ID`應該開頭不是數字。 一般的做法是新增**識別碼**GUID 的字串表示。

- `Version`︰ 將此項目的值為**2.0**。 需要此值。

- `IssueInstant`︰ 這是`DateTime`字串國際標準時間 (UTC) 的值與[來回格式 (「 o 」)](https://msdn.microsoft.com/library/az4se3k1.aspx)。 Azure AD 此類型的值，但不會執行。

- `Consent`， `Destination`，`NotOnOrAfter`和`Reason`屬性會略過，如果其包含在`LogoutRequest`項目。

### <a name="issuer"></a>發行者

`Issuer`中的項目`LogoutRequest`必須確實符合**ServicePrincipalNames**雲端服務中的其中一個 Azure AD 中。 一般而言，這是設定**應用程式識別碼 URI**應用程式註冊期間所指定。

### <a name="nameid"></a>NameID

值的`NameID`完全符合的項目，必須`NameID`正在登出的使用者。
## <a name="logoutresponse"></a>LogoutResponse

Azure AD 傳送`LogoutResponse`來回應`LogoutRequest`項目。 下列摘錄顯示範例`LogoutResponse`。

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse

Azure AD 集`ID`，`Version`和`IssueInstant`值在`LogoutResponse`項目。 它也會將`InResponseTo`的值的項目`ID`屬性`LogoutRequest`的 elicited 回應。

### <a name="issuer"></a>發行者

Azure AD 將此值設為`https://login.microsoftonline.com/<TenantIdGUID>/`位置<TenantIdGUID>Azure AD 租用戶的租用戶 id。

若要評估的值`Issuer`項目、 使用應用程式註冊期間所提供的**應用程式識別碼 URI**的值。

### <a name="status"></a>狀態

Azure AD 用途`StatusCode`中的項目`Status`表示的成功或失敗教具借出的項目。 當教具借出失敗時，`StatusCode`項目也可以包含自訂錯誤訊息。
