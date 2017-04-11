<properties
    pageTitle="如何保護 Web API 後的端 Azure Active Directory 與 API 管理"
    description="瞭解如何保護 Web API 後的端 Azure Active Directory 與 API 管理。" 
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

# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>如何保護 Web API 後的端 Azure Active Directory 與 API 管理

以下影片示範如何建立 Web API 後端和 Azure Active Directory 和管理的 API 使用 OAuth 2.0 通訊協定加以保護。  本文提供概觀，以及其他資訊，如視訊中的步驟。 此 24 分鐘影片教您如何以︰

-   建立 Web API 後端及保護其安全性與 AAD-1:30 開始
-   將 API 管理-7:10 開始匯入 API
-   設定來電 API-開始，9:09 開發人員入口網站
-   設定桌面應用程式撥打 API-開頭 18:08
-   設定 JWT 驗證原則，以預先授權要求-20:47 開始

>[AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

## <a name="create-an-azure-ad-directory"></a>建立 Azure AD 目錄

若要保護您網站的 API 備份使用 Azure Active Directory 您必須先 AAD 租用戶。 在這段影片使用名為**APIMDemo**租用戶。 若要建立 AAD 租用戶，登入[Azure 傳統入口網站](https://manage.windowsazure.com)，按一下 [**新增**->**應用程式服務**->**Active Directory**->**目錄**->**建立自訂**。 

![Azure Active Directory][api-management-create-aad-menu]

在此範例中名為**APIMDemo**的目錄會建立一個名為**DemoAPIM.onmicrosoft.com**的預設網域。 在視訊會使用此目錄。

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>建立 Web API 服務受到 Azure Active Directory

在此步驟中，網頁 API 後端使用 Visual Studio 2013 建立。 此步驟中的視訊開頭 1:30。 若要建立 Web API 後端專案 Visual Studio 中的按一下 [**檔案**->**新增**->**專案**，並從**網站**範本] 清單中選擇**ASP.NET Web 應用程式**。 在這段影片專案名稱為**APIMAADDemo**。 按一下**[確定**] 以建立專案。 

![Visual Studio][api-management-new-web-app]

按一下 [ **Web API** ，從 [**選取範本] 清單中**建立網頁 API 專案]。 若要設定 Azure 目錄驗證按一下 [**變更驗證**]。

![新的專案][api-management-new-project]

按一下**組織的帳戶**]，指定您 AAD 租用戶的**網域**。 在此範例中的網域已**DemoAPIM.onmicrosoft.com**。 從您的目錄中的 [**網域**] 索引標籤，您可以取得您的目錄的網域。

![網域][api-management-aad-domains]

**變更驗證**] 對話方塊中設定所需的設定，然後按一下**[確定]**。

![變更驗證][api-management-change-authentication]

當您按一下**[確定]**時 Visual Studio 會嘗試使用 Azure AD 目錄註冊您的應用程式與您可能會提示您登入 Visual Studio。 為您的目錄使用系統管理員帳戶登入。

![登入 Visual Studio][api-management-sign-in-vidual-studio]

若要設定**在雲端的主機**Azure Web API 核取方塊為此專案，然後按一下**[確定]**。

![新的專案][api-management-new-project-cloud]

您可能會提示您登入 Azure，然後您可以設定 Web 應用程式。

![設定][api-management-configure-web-app]

在此範例會指定新的**應用程式服務方案**名為**APIMAADDemo** 。

按一下 [設定 Web 應用程式，並建立專案的**[確定]** 。

## <a name="add-the-code-to-the-web-api-project"></a>將程式碼新增至網頁 API 專案

視訊的下一個步驟會新增至網頁 API 專案的程式碼。 此步驟會在 4:35 開始。

在此範例中的網頁 API 實作使用模型和控制站基本計算機服務。 若要新增服務的模型，以滑鼠右鍵按一下 [**模型****總管**] 中，選擇 [**新增**]**類別**。 命名類別`CalcInput`並按一下 [**新增**]。

新增下列`using`陳述式的頂端`CalcInput.cs`檔案。

    using Newtonsoft.Json;

 下列程式碼取代所產生的類別。

    public class CalcInput
    {
        [JsonProperty(PropertyName = "a")]
        public int a;

        [JsonProperty(PropertyName = "b")]
        public int b;
    }

**控制站****方案總管**] 中以滑鼠右鍵按一下，然後選擇 [**新增**->**控制器**。 選擇**Web API 2 控制器空的**然後按一下 [**新增**]。 輸入**CalcController**控制器名稱，然後按一下 [**新增**。

![新增控制器][api-management-add-controller]

新增下列`using`陳述式的頂端`CalcController.cs`檔案。

    using System.IO;
    using System.Web;
    using APIMAADDemo.Models;

取代下列程式碼的產生的控制器類別。 將此程式碼實作`Add`， `Subtract`， `Multiply`，及`Divide`基本計算機 API 的作業。

    [Authorize]
    public class CalcController : ApiController
    {
        [Route("api/add")]
        [HttpGet]
        public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/sub")]
        [HttpGet]
        public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/mul")]
        [HttpGet]
        public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }

        [Route("api/div")]
        [HttpGet]
        public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
        {
            string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
            HttpResponseMessage response = Request.CreateResponse();
            response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
            return response;
        }
    }

按**F6** ，若要建立並確認解決方案。

## <a name="publish-the-project-to-azure"></a>將專案發佈至 Azure

在此步驟中 Visual Studio 專案發佈至 Azure。 此步驟中的視訊開頭 5:45。

若要將專案發佈至 Azure 中，以滑鼠右鍵按一下 [在 Visual Studio **APIMAADDemo**專案，並選擇 [**發佈**。 保留預設設定**發佈網站**] 對話方塊中，按一下 [**發佈**]。

![網頁發佈][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>授與權限的 Azure AD 後端 service 應用程式

新的應用程式後端服務會建立在 Azure AD 目錄，設定及發佈程序 Web API 專案的一部分。 在此步驟中的視訊，請開始，6:13，權限授與網站的 API 後端。

![應用程式][api-management-aad-backend-app]

按一下要設定的必要權限的應用程式名稱。 瀏覽至 [**設定**] 索引標籤，然後向**其他應用程式的權限**] 區段下捲動。 按一下 [**應用程式權限****在 Windows** **Azure Active Directory**旁邊的下拉式核取方塊為**已讀取目錄資料**，與按一下 [**儲存**]。

![新增權限][api-management-aad-add-permissions]

>[AZURE.NOTE] 如果**Windows** **Azure Active Directory**沒有列出其他應用程式的權限] 下，按一下 [**新增應用程式**，然後從清單中新增。

記下**應用程式識別碼 URI**用於後續步驟 API 管理開發人員入口網站設定 Azure AD 應用程式時。

![應用程式識別碼 URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>網路 API 匯入 API 管理

Api 的存取透過 Azure 傳統入口網站的 API publisher 入口網站的設定。 若要達到 publisher 入口網站，按一下 [在您的 API 管理服務 Azure 傳統入口網站**管理**]。 如果您尚未建立 API 管理服務執行個體，請參閱[建立 API 管理服務執行個體][]，[管理您的第一個 API][]教學課程中。

![Publisher 入口網站][api-management-management-console]

作業可以[手動新增至 Api](api-management-howto-add-operations.md)，或可以匯入。 在這段影片中，則 Swagger 格式開始，6:40 會匯入作業。

建立一個名為檔案`calcapi.json`以下列內容並將其儲存至您的電腦。 確定`host`至您網站的 API 的後端屬性點。 在此範例中`"host": "apimaaddemo.azurewebsites.net"`使用。

{「 swagger 」: 「 2.0]、 [資訊]: {「 標題 」: 「 計算機 」 「 描述 」: 「 Arithmetics 透過 HTTP ！ 」，[版本]: 「 1.0 」}，「 主機 」: 「 apimaaddemo.azurewebsites.net 」、 「 基本路徑 」: 」 / api 」，「 配置 」: [「 http 」] 」 路徑 」: {」 / 新增嗎？ = {} & b = {b} 」: {「 取得 」: {「 描述 」: 」 回應使用加總兩個數字。 」 「 operationId 」: 」 新增兩個整數 」、 「 參數 」: [{"名稱":"a"，"in": 「 查詢 」，「 描述 」: 「 第一個運算元。 預設值是<code>51</code>。 」、 「 必要 」: true 時，「 預設 」: 「 51 「，」 列舉 」: [「 51 」]}，{"名稱":"b"，"in": 「 查詢 」，「 描述 」: 「 第二個運算元。 預設值是<code>49</code>。 」、 「 必要 」: true 時，「 預設 」: 「 49 「，」 列舉 「: [「 49 「]}]，「 回應 」: {}}}，「 / sub?a = {} & b = {b} 「: {「 取得 」: {「 描述 」: 「 回應差異，之間兩個數字。 」 「 operationId 「: 「 減去兩個整數 」、 「 參數 」: [{"名稱":"a"，"in": 「 查詢 」，「 描述 」: 「 第一個運算元。 預設值是<code>100</code>。 」、 「 必要 」: true 時，「 預設 」: 「 100 「，」 列舉]: ["100"]}，{"名稱":"b"，「 中 」: 「 查詢 」，「 描述 」: 「 第二個運算元。 預設值是<code>50</code>。 」、 「 必要 」: true 時，「 預設 」: 「 50 「，」 列舉 」: [「 50 」]}]，「 回應 」: {}}}，」 / div?a = {} & b = {b} 」: {「 取得 」: {「 描述 」: 「 商數為兩個數字與回應 」，「 operationId 」: 「 除以兩個整數 」、 「 參數 」: [{"名稱":"a"，"in": 「 查詢 」，「 描述 」: 「 第一個運算元。 預設值是<code>100</code>。 」、 「 必要 」: true 時，「 預設 」: 「 100 「，」 列舉]: ["100"]}，{"名稱":"b"，「 中 」: 「 查詢 」，「 描述 」: 「 第二個運算元。 預設值是<code>20</code>。 」、 「 必要 」: true 時，「 預設 」: 「 20 「，」 列舉 」: [「 20 」]}]，「 回覆 」: {}}}，」 / mul?a = {} & b = {b} 」: {「 取得 」: {「 描述 」: 「 回應產品的兩個數字。 」 「 operationId 」: 「 乘以兩個整數 」、 「 參數 」: [{"名稱":"a"，"in": 「 查詢 」，「 描述 」: 「 第一個運算元。 預設值是<code>20</code>。 」 「 必要 」、: true 時，「 預設 」: 「 20 「，」 列舉 」: [「 20 」]}，{"名稱":"b"，"in": 「 查詢 」，「 描述 」: 「 第二個運算元。 預設值是<code>5</code>。 」、 「 必要 」: true 時，「 預設 」:"5"，"列舉 」: ["5"]}]，「 回應 」: {}}}}}

要匯入計算機 API，按一下 [ **Api**從**API 管理**左側功能表上，然後再按一下 [**匯入的 API**。

![匯入 API] 按鈕][api-management-import-api]

執行下列步驟，以設定 API 計算機。

1. 按一下 [**從檔案**、 瀏覽至`calculator.json`檔案儲存，然後按一下 [ **Swagger** ] 選項按鈕。
2. 輸入**網址 API 尾碼**] 文字方塊中的**計算**。
3. 在 [**產品 （選用）** ] 方塊中按一下，然後選擇 [**入門**。
4. 按一下 [匯入 API 的 [**儲存**]。

![新增新的 API][api-management-import-new-api]

一旦匯入 API，API 的 [摘要] 頁面會顯示在 publisher 入口網站。

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>從開發人員入口網站不成功︰ 呼叫 API

此時，API 匯入 API 管理，但無法尚未成功從呼叫開發人員入口網站因為 Azure AD 驗證受保護的後端服務。 開始使用下列步驟 7:40 影片所示。

按一下 [從 publisher 入口網站右上方的**開發人員入口網站**]。

![開發人員入口網站][api-management-developer-portal-menu]

按一下**Api** ，然後按一下 [**計算機**API。

![開發人員入口網站][api-management-dev-portal-apis]

按一下 [**試試看**]。

![試試看][api-management-dev-portal-try-it]

按一下 [**傳送**]，並注意**401 未經授權**的回應狀態。

![傳送][api-management-dev-portal-send-401]

要求沒有，因為後端 API 受到 Azure Active Directory。 成功呼叫 API 開發人員之前必須授權使用 OAuth 2.0 的開發人員設定入口網站。 此程序是由下列各節所述。

## <a name="register-the-developer-portal-as-an-aad-application"></a>註冊為 AAD 應用程式的開發人員入口網站

設定開發人員入口網站，來授權使用 OAuth 2.0 的開發人員的第一個步驟是註冊為 AAD 應用程式的開發人員入口網站。 示範開始，8:27 影片。

瀏覽至 Azure AD 租用戶從這段影片，在此範例中**APIMDemo**的第一個步驟，然後瀏覽至 [**應用程式**] 索引標籤。

![新的應用程式][api-management-aad-new-application-devportal]

按一下 [建立新的 Azure Active Directory 應用程式中，[**新增**] 按鈕，然後選擇 [**新增組織內部開發的應用程式**。

![新的應用程式][api-management-new-aad-application-menu]

選擇**Web 應用程式及/或 Web API**、 輸入名稱，然後按一下下一步的箭號。 在此範例會使用**APIMDeveloperPortal** 。

![新的應用程式][api-management-aad-new-application-devportal-1]

**登入**url 輸入您的 API 管理服務的 URL 與附加`/signin`。 在此範例會使用**https://contoso5.portal.azure-api.net/signin **。

**應用程式識別碼**url 輸入您的 API 管理服務的 URL，然後新增一些唯一的字元。 在此範例使用**https://contoso5.portal.azure-api.net/dp**而這些可所要的任何字元。 設定所需的**應用程式屬性**，請按一下 [核取記號，以建立應用程式]。

![新的應用程式][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>設定 API 管理 OAuth 2.0 授權伺服器

下一步是在 API 管理設定 OAuth 2.0 授權伺服器。 開始，9:43 影片示範此步驟。

按一下 [**安全性**]，請從左側的 [API 管理] 功能表，按一下**OAuth 2.0**，，再按一下 [**新增授權**伺服器。

![新增授權伺服器][api-management-add-authorization-server]

在 [**名稱**與**描述**] 欄位中輸入的名稱，以及選擇性的描述。 這些欄位用來識別 OAuth 2.0 授權伺服器的 API 管理服務執行個體中。 在此範例會使用**授權伺服器示範**。 稍後當您指定要用於驗證的 API 的 OAuth 2.0 伺服器時，您會選取此名稱。

**用戶端註冊頁面的 URL**輸入版面配置區值，例如`http://localhost`。  **用戶端註冊頁面的 URL**指向使用者可以用來建立及設定自己的帳戶支援使用者管理帳戶的 OAuth 2.0 提供者的頁面。 在此範例中的使用者不執行建立及設定自己的帳戶，因此會使用版面配置區。

![新增授權伺服器][api-management-add-authorization-server-1]

接下來，指定**授權端點 URL** ] 與 [**權杖端點 URL**。

![授權伺服器][api-management-add-authorization-server-1a]

從 [**應用程式結束點**] 頁面，您建立的開發人員入口網站的 AAD 應用程式可以擷取這些值。 存取端點瀏覽至 AAD 應用程式的 [**設定**] 索引標籤，然後按一下 [**檢視結束點**。

![應用程式][api-management-aad-devportal-application]

![檢視端點][api-management-aad-view-endpoints]

複製**OAuth 2.0 授權結束點**，並將它貼到 [**授權端點 URL** ] 文字方塊。

![新增授權伺服器][api-management-add-authorization-server-2]

複製**OAuth 2.0 token 結束點**，並將它貼到 [**權杖端點 URL** ] 文字方塊。

![新增授權伺服器][api-management-add-authorization-server-2a]

除了 token 端點中貼上，新增其他本文參數名稱為**資源**和 Visual Studio 專案發佈的後端服務時所建立的值，使用**應用程式識別碼 URI** AAD 應用程式。

![應用程式識別碼 URI][api-management-aad-sso-uri]

接下來，指定的用戶端認證。 這些是您要存取，請在這種情況下的後端服務資源的認證。

![用戶端憑證][api-management-client-credentials]

若要取得**的用戶端識別碼**，瀏覽至 [**設定**] 索引標籤的後端服務的 AAD 應用程式，然後複製 [**用戶端識別碼**。

以存取**用戶端私人**按一下**選取期間**的下拉式**鍵**] 區段中並指定時間間隔。 在此範例會使用 1 年。

![用戶端識別碼][api-management-aad-client-id]

按一下 [儲存設定並顯示 [索引鍵的 [**儲存**]。 

>[AZURE.IMPORTANT] 記下此鍵。 當您關閉 Azure Active Directory 的 [設定] 視窗時，不能再顯示索引鍵。

複製到剪貼簿的機碼、 切換回 publisher 入口網站、 貼入鍵**用戶端 [私人**] 文字方塊中，並按一下 [**儲存**]。

![新增授權伺服器][api-management-add-authorization-server-3]

緊接的用戶端認證是授權的程式碼授與。 複製此授權程式碼，請切換至 Azure AD 開發人員入口網站應用程式設定] 頁面上，並貼上授權授與**回覆 URL** ] 欄位中，然後再按一次 [**儲存**。

![回覆 URL][api-management-aad-reply-url]

下一步是設定為開發人員入口網站 AAD 應用程式的權限。 按一下 [**應用程式權限**，並核取方塊為**已讀取目錄資料**。 按一下 [儲存此變更，[**儲存**]，然後按一下 [**新增應用程式**。

![新增權限][api-management-add-devportal-permissions]

按一下 [搜尋] 圖示、 [開始] 方塊中輸入**APIM**選取**APIMAADDemo**，然後按一下核取記號以儲存。

![新增權限][api-management-aad-add-app-permissions]

按一下**APIMAADDemo**的**委派權限**和**存取 APIMAADDemo**，核取方塊，按一下 [**儲存**]。 這個選項可讓開發人員入口網站應用程式，來存取後端服務。

![新增權限][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>啟用計算機 API OAuth 2.0 使用者的授權

現在 OAuth 2.0 伺服器設定，您可以指定其在您的 API 的安全性設定]。 此步驟會示範影片開始，14:30。

在左功能表上，按一下 [ **Api** ，然後按一下 [檢視並設定其設定的**計算機**。

![計算機 API][api-management-calc-api]

瀏覽至 [**安全性**] 索引標籤、 核取 [ **OAuth 2.0**核取方塊、 選取所要的授權伺服器**授權伺服器**下拉箭號，然後按一下 [**儲存**。

![計算機 API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>成功的計算機 API 來電開發人員入口網站

既然 OAuth 2.0 授權設定 API 上時，其操作可以順利稱為從開發人員中心。 此步驟示範 15:00 啟動視訊。

瀏覽回計算機服務，在開發人員入口網站中的 [**新增兩個整數**作業，然後按一下 [**試試看**]。 請注意新的項目對應到您剛才新增的授權伺服器的 [**授權**] 區段中。

![計算機 API][api-management-calc-authorization-server]

從 [授權] 下拉式清單中選取**授權程式碼**，然後輸入要使用的帳戶認證。 如果您已經登入可能不會提示您的帳戶。

![計算機 API][api-management-devportal-authorization-code]

按一下 [**傳送**]，並記下的**[回覆] 狀態** **200 確定**和回應內容中作業的結果。

![計算機 API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>設定桌面應用程式撥打 API

在視訊中的下一個程序開始編號，16:30，並設定撥打 API 簡單的桌面應用程式。 第一步是在 Azure AD 註冊桌面應用程式，並提供存取目錄，並且後端服務。 在 18:25 有呼叫計算機 API 的作業的桌面應用程式的示範。

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>設定 JWT 驗證原則，以預先授權要求

在視訊中的最後一個程序在 20:48 啟動，並說明如何使用 [[驗證 JWT](https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT)原則預先驗證存取權杖，每個傳入要求的授權要求。 如果驗證 JWT 原則不驗證要求時，要求的 API 管理封鎖，並不會沿傳遞至後端。

    <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
        <openid-config url="https://login.windows.net/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
            </claim>
        </required-claims>
    </validate-jwt>

另一個設定和使用此原則的示範，請參閱[雲端封面片段 177︰ 更多 API 管理功能](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/)和快轉至 13:50。 快速轉寄至 15:00 若要查看設定原則編輯器] 中的原則再從開發人員入口網站的並不需要的授權權杖呼叫作業的示範 18:50。

## <a name="next-steps"></a>後續步驟
-   查看 API 管理相關的更多[影片](https://azure.microsoft.com/documentation/videos/index/?services=api-management)。
-   保護您的後端服務的其他方法，請參閱[相互憑證驗證](api-management-howto-mutual-certificates.md)及[連線透過 VPN 或 ExpressRoute](api-management-howto-setup-vpn.md)。

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[建立 API 管理服務執行個體]: api-management-get-started.md#create-service-instance
[管理您的第一個 API]: api-management-get-started.md
