<properties 
    pageTitle="如何設定 TLS 相互驗證的 Web 應用程式" 
    description="瞭解如何設定 web 應用程式上 TLS 使用用戶端憑證驗證]。" 
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/08/2016" 
    ms.author="naziml"/>    

# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>如何設定 TLS 相互驗證的 Web 應用程式

## <a name="overview"></a>概觀 ##
您可以藉由啟用不同類型的驗證其 Azure web 應用程式限制存取。 若要執行此作業的其中一個方法是透過 TLS/SSL 要求時，使用用戶端憑證進行驗證。 這種方式稱為 TLS 相互驗證或用戶端憑證驗證]，然後這份文件將詳細說明如何設定 web 應用程式使用用戶端憑證驗證]。

> **附註︰**如果您是在 HTTP 並不 HTTPS 存取您的網站，就不會收到任何用戶端憑證。 因此，如果您的應用程式需要用戶端憑證您應該不允許要求您的應用程式透過 HTTP。


[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="configure-web-app-for-client-certificate-authentication"></a>設定 Web 應用程式的用戶端憑證驗證 ##
若要設定您的 web 應用程式需要您需要新增 clientCertEnabled 網站設定 web 應用程式，並將其設為 true 的用戶端憑證。 這項設定目前提供透過管理體驗，在入口網站，並不需要 REST API 來完成這項作業。

您可以使用[ARMClient 工具](https://github.com/projectkudu/ARMClient)，可輕鬆製作 REST API 通話。 您登入工具之後，您將需要執行下列命令︰

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose
    
使用 web 應用程式的資訊取代在 {} 中的所有項目，並建立檔案稱為與下列 JSON enableclientcert.json 內容︰

> {[位置]: 「 我 Web 應用程式的位置]，   
>   [內容]: {  
>     「 clientCertEnabled 」: true}}  

請確定您的 [位置] 的值變更為您的 web 應用程式所在的任何地方例如北美美國中部或西部美國等。

> **附註︰**如果您是從 Powershell 執行 ARMClient，您必須逸出@符號後的刻度 JSON 檔案 」。

## <a name="accessing-the-client-certificate-from-your-web-app"></a>從您的 Web 應用程式存取的用戶端憑證 ##
如果您使用的 ASP.NET，設定您的應用程式使用用戶端憑證驗證憑證可透過**HttpRequest.ClientCertificate**屬性。 其他應用程式堆疊的用戶端憑證將可在您的應用程式，透過 base64 編碼中值的 「 X-ARR-ClientCert 」 要求標頭。 您的應用程式可以從這個值建立憑證，然後依應用程式中的驗證及授權用途。

## <a name="special-considerations-for-certificate-validation"></a>憑證驗證的特殊考量 ##
傳送至應用程式的用戶端憑證不會經過任何驗證 Azure Web 應用程式平台。 驗證這個憑證負責的 web 應用程式。 以下是範例 ASP.NET 程式碼，驗證憑證內容進行驗證。

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;
                
                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;
                
                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
