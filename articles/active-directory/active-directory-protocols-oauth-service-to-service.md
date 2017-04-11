<properties
    pageTitle="Azure AD 服務至服務驗證使用 OAuth2.0 |Microsoft Azure"
    description="本文將說明如何使用 HTTP 訊息實作服務至服務驗證使用 OAuth2.0 用戶端的認證授與流程。"
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

# <a name="service-to-service-calls-using-client-credentials"></a>使用用戶端認證的服務通話的服務

[OAuth 2.0 用戶端認證授與流程允許 web 服務 （*機密的用戶端*），以驗證呼叫其他 web 服務，而非模擬使用者時使用自己的認證。 在此案例中，通常的用戶端是中間層 web 服務、 精靈服務或網站。

## <a name="client-credentials-grant-flow-diagram"></a>用戶端憑證授與流程圖

下圖說明如何用戶端憑證授與 Azure Active Directory (Azure AD) 中的流程運作。

![OAuth2.0 用戶端認證授與流程](media/active-directory-protocols-oauth-service-to-service/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. 用戶端應用程式至 Azure AD 權杖發行端點驗證，並要求存取權杖。
2. Azure AD 權杖發行端點問題存取權杖。
3. 存取權杖會用於驗證安全的資源。
4. 在 web 應用程式會傳回從安全的資源的資料。

## <a name="register-the-services-in-azure-ad"></a>Azure AD 在註冊服務

註冊 Azure Active Directory (Azure AD) 中的呼叫的服務與接收服務。 如需詳細指示，請參閱[新增、 更新，及移除應用程式](active-directory-integrating-applications.md#BKMK_Native)

## <a name="request-an-access-token"></a>要求存取權杖

若要要求存取權杖，請使用 [HTTP 張貼到租用戶專屬 Azure AD 結束點。

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>服務-服務存取權杖要求

服務-服務存取權杖要求包含下列參數。

| 參數 | | 描述 |
|-----------|------|------------|
| response_type | 所需 | 指定要求的回應類型。 用戶端認證授與的流程，值必須是**client_credentials**。|
| client_id | 所需 | 指定呼叫的 web 服務的 Azure AD 用戶端識別碼。 若要尋找呼叫應用程式的用戶端識別碼，Azure 管理入口網站中，按一下 [ **Active Directory**目錄，按一下 [應用程式，然後按一下然後按一下 [**設定**。|
| client_secret | 所需 |  輸入金鑰中 Azure AD 註冊呼叫的 web 服務。 若要建立索引鍵，Azure 管理入口網站中，按一下 [ **Active Directory**目錄、 按一下 [應用程式，然後按一下然後按一下 [**設定**。 |
| 資源 | 所需 | 輸入應用程式識別碼 URI 接收 web 服務。 若要尋找應用程式識別碼 URI Azure 管理入口網站中，按一下 [ **Active Directory**目錄，按一下 [應用程式，然後按一下然後按一下 [**設定**。 |

## <a name="example"></a>範例

下列 HTTP 文章要求存取權杖 https://service.contoso.com/ web 服務。 `client_id`識別之 web 服務的要求存取權杖。

```
POST contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

## <a name="service-to-service-access-token-response"></a>服務-服務存取權杖回應

成功回應包含下列參數為 JSON OAuth 2.0 回應。

| 參數   | 描述 |
|-------------|-------------|
|access_token |要求的存取權杖。 呼叫 web 服務可以使用此 token 加驗證接收 web 服務。 |
|access_type  | 表示的權杖類型的值。 Azure AD 支援的唯一類型是**承載者**。 如需承載者權杖相關資訊，請參閱[OAuth 2.0 授權架構︰ 承載者權杖使用量 (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt)。
|expires_in   | 多久存取權杖是生效 （秒）。|
|expires_on   |存取權杖何時到期時間。 日期表示的秒數為從 1970年-01-01T0:0:0Z UTC 直到的到期時間。 此值用來決定的快取的權杖存留時間。 |
|資源     | 接收 web 服務應用程式識別碼 URI。 |

## <a name="example"></a>範例

下列範例顯示存取權杖到 web 服務的要求的成功回應。

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>另請參閱

* [Azure AD 中 OAuth 2.0](active-directory-protocols-oauth-code.md)
