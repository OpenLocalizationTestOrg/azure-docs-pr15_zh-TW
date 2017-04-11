<properties 
    pageTitle="如何保護後端服務使用的用戶端憑證驗證 Azure API 管理" 
    description="瞭解如何保護 Azure API 管理中使用用戶端憑證驗證的後端服務。" 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>如何保護後端服務使用的用戶端憑證驗證 Azure API 管理

API 管理提供的功能安全存取後端服務的 API 使用用戶端憑證。 本指南會顯示如何管理憑證在 API publisher 入口網站，以及如何設定 API 來存取後端服務使用的憑證。

如需管理使用 API 管理 REST API 的憑證資訊，請參閱[Azure API 管理 REST API 憑證實體][]。

## <a name="prerequisites"></a>先決條件

本指南為您示範如何設定 API 管理服務執行個體來存取後端服務的 API 使用用戶端憑證驗證。 本主題中的步驟前，您應該後端服務設定用戶端憑證驗證 （[若要設定 Azure 網站中的憑證驗證，請參閱本文][]），並在 API 管理 publisher 入口網站上傳的憑證的存取權的憑證和密碼]。

## <a name="step1"></a>上傳的用戶端憑證

若要開始，按一下 [**管理**您的 API 管理服務 Azure 傳統入口網站中。 會帶您到 API 管理 publisher 入口網站。

![API Publisher 入口網站][api-management-management-console]

>如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，以[開始使用 Azure API 管理][]教學課程中。

按一下 [**安全性**]，請從左側的 [ **API 管理**] 功能表，然後按一下 [**用戶端憑證**。

![用戶端憑證][api-management-security-client-certificates]

若要上傳新的憑證，請按一下 [**上傳的憑證**。

![上傳的憑證][api-management-upload-certificate]

瀏覽至您的憑證，然後輸入憑證的密碼。

>憑證必須**.pfx**格式。 允許自我簽署的憑證。

![上傳的憑證][api-management-upload-certificate-form]

按一下 [上傳憑證的 [**上傳**]。

>這次驗證憑證密碼。 如果是不正確的會顯示錯誤訊息。

![上傳的憑證][api-management-certificate-uploaded]

上傳憑證後會顯示在**用戶端憑證**] 索引標籤。 如果您有多個憑證，請記下主旨、 指紋，這用來設定的 API 使用憑證，如下列[設定的 API 使用閘道驗證的用戶端憑證][]一節時，請選取憑證的第四個字元。

>若要關閉憑證鏈結驗證使用時，例如，自我簽署的憑證，請遵循此常見問題集[項目](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)所述的步驟。

## <a name="step1a"></a>刪除用戶端憑證

若要刪除憑證，按一下所需的憑證] 旁的 [**刪除**]。

![刪除憑證][api-management-certificate-delete]

按一下 [**是]，將其刪除**以確認。

![確認刪除][api-management-confirm-delete]

如果是由 API 使用憑證，就會顯示警告畫面。 若要刪除憑證必須先憑證移除任何 Api，會設定為使用它。

![確認刪除][api-management-confirm-delete-policy]

## <a name="step2"></a>設定使用用戶端憑證驗證閘道器的 API

按一下 [從**API 管理**左側功能表上的 [ **Api**所需的 API 的名稱，然後按一下 [**安全性**] 索引標籤。

![API 安全性][api-management-api-security]

從 [**認證**] 下拉式清單中，選取**用戶端憑證**。

![用戶端憑證][api-management-mutual-certificates]

從**用戶端憑證**下拉式清單中選取所需的憑證。 如果有多個憑證，您可以查看主旨或指紋如前一節所述的第四個字元，來判斷正確的憑證。

![選取憑證][api-management-select-certificate]

按一下 [儲存設定變更對 API 的 [**儲存**]。

>這項變更會立即生效，並的來電至] 作業的 API 使用憑證驗證後端伺服器上。

![儲存 API 的變更][api-management-save-api]

>閘道器驗證後端服務的 API 指定憑證時，其原則的規範用的 API，並且可以原則編輯器] 中檢視。

![憑證原則][api-management-certificate-policy]

## <a name="next-steps"></a>後續步驟

如需其他方式保護您的後端服務，例如 HTTP 基本或共用私人驗證]，請參閱以下的影片。

> [AZURE.VIDEO last-mile-security]

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[開始使用 Azure API 管理]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance

[Azure API 管理 REST API 憑證實體]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[若要設定 Azure 網站中的憑證驗證，請參閱本文]: https://azure.microsoft.com/en-us/documentation/articles/app-service-web-configure-tls-mutual-auth/

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[設定使用用戶端憑證驗證閘道器的 API]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps


 
