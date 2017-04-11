<properties
    pageTitle="Azure API 管理中的自訂快取"
    description="瞭解如何將快取 Azure API 管理中的索引鍵的項目"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="custom-caching-in-azure-api-management"></a>Azure API 管理中的自訂快取
Azure API 管理服務有內建支援[HTTP 回應快取](api-management-howto-cache.md)索引鍵使用資源的 URL。 索引鍵可使用的要求標頭修改`vary-by`屬性。 這是很適合用於快取整個 HTTP 回應 （又稱表示），但有時很有用只快取表示的一部分。 新的[快取查閱值](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey)和[快取市集值](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey)原則提供的功能來儲存及擷取任意從原則定義中的資料。 這項功能也將值加入先前引入的[傳送邀請](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)原則因為您現在可以快取的外部服務回應。

## <a name="architecture"></a>架構  
這麼一來，您仍可以存取相同的多個單位為不按比例縮放最快取資料，API 管理服務就會使用共用每個租用戶資料快取。 不過，當您使用多區域部署時有獨立快取中每個區域。 因此，很重要不視為是唯一的某部分的資訊來源的資料存放區中的快取。 如果您並未，並稍後決定要利用多區域部署，客戶的旅行使用者可能會快取資料遺失存取。

## <a name="fragment-caching"></a>片段快取
有某些情況下，其回應傳回包含昂貴決定，還會保持最新合理的時間資料的某些部分。 作為範例，請考慮建立的航空公司提供與航班保留項目、 航班狀態等相關資訊的服務。如果使用者是航空公司點程式的成員，他們也有其目前狀態和累積里程數的相關資訊。 此使用者的相關資訊可能儲存在不同系統中，但可能會希望包含在傳回航班狀態與預定的回應。 這可以使用稱為片段快取程序。 從原始伺服器，使用某種權杖指出要插入的使用者的相關資訊在哪裡可以傳回的主要表示。 

請考慮下列的後端 API JSON 回應。


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

和次要資源在`/userprofile/{userid}`，看起來像，

     { "username" : "Bob Smith", "Status" : "Gold" }

若要決定要包含的適當的使用者資訊，我們要使用者身分識別。 這個機制是實作相依。 例如，我使用的`Subject`的宣告`JWT`token。 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

我們會將儲存此`enduserid`以供日後使用的內容變數中的值。 下一步是決定是否先前要求已擷取的使用者資訊並儲存至快取。 我們使用這個`cache-lookup-value`原則。

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

如果沒有任何項目對應到最重要的值，則無法快取中`userprofile`內容變數將會建立。 我們會檢查的查閱使用成功`choose`控制流程原則。

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


如果`userprofile`內容變數不存在，然後我們必須以擷取其 HTTP 要求。

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

我們使用`enduserid`建構使用者設定檔資源的 URL。 一旦回應，我們可以拉出回應本文並將其儲存至內容變數。

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

若要避免我們有相同的使用者進行另一個要求時，進行此 HTTP 要求，我們可以儲存快取中的使用者設定檔。

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

我們使用完全相同的金鑰我們原本嘗試擷取它的快取中儲存的值。 工期我們選擇儲存的值應該為基礎的方式通常的資訊變更及如何容錯使用者是以過期的資訊。 

請務必瞭解的擷取從快取出程序、 網路要求及還可能可以新增百毫秒的要求。 優點是在決定使用者設定檔資訊花比因為需要執行查詢或彙總的資訊，從多個的後端資料庫。

程序中的最後一個步驟是使用我們的使用者設定檔資訊更新傳回的回應。

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

我選擇加入括住權杖，好讓回應取代並不會發生，即使是仍然有效 JSON。 這是主要讓偵錯更容易。

一旦您在一起合併所有這些步驟，最後的結果會是原則，看起來像下列項目。

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

此快取的方法是主要用於網站位置 HTML 組成伺服器端，讓它可以呈現為單一頁面。 不過，它也可以用來在用戶端無法進行的動作用戶端的 Api 側邊 HTTP 快取或不希望將責任放在用戶端。

也可以使用快取伺服器意指後端網頁伺服器上完成此相同的片段快取，不過，使用可執行此工作的 API 管理服務，不妨使用快取的片段來自不同後端比主要的回應。

## <a name="transparent-versioning"></a>透明的版本設定
是一次可支援 API 的多個不同的實作版本的一般做法。 這可能是支援不同環境中的，例如開發、 測試、 生產等，或它可能支援較舊版本的 API 的 API 消費者移轉到較新版本的時間。 

處理此文件，而不需要用戶端開發人員若要變更從 Url 的其中一個方法`/v1/customers`至`/v2/customers`是儲存在消費者的設定檔資料哪個版本的 API 他們目前想要使用，然後呼叫適當的後端 URL。 若要決定要為特定的用戶端呼叫正確的後端 URL，就需要一些設定資料的查詢。 快取此設定資料，我們可以最小化執行此查閱效能產生負面的影響。

第一步是決定用來設定所要的版本的識別碼。 在此範例中，選取了 [建立關聯訂閱產品金鑰的版本。 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

然後，我們會執行快取查閱請參閱是否我們已擷取的所需的用戶端版本。

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

然後我們檢查是否我們找不到其快取中。

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

如果我們未然後我們跳並擷取它。

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

回應本文從擷取文字回應。

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

將其儲存回快取以供日後使用。

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

及最後更新後端 URL，選取想要用戶端的服務版本。

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

完全原則如下所示。

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


啟用 API 消費者無障礙地控制後端的是哪個版本用戶端而不需要更新並重新部署用戶端存取是一種優雅的解決方案，解決許多 API 版本設定問題。

## <a name="tenant-isolation"></a>租用戶隔離

在較大的多租用戶部署有些公司會建立租用戶的另一個的群組的後端硬體相異部署上。 這會最小化之硬體的問題，在後端、 受到客戶的數字。 也可以讓即將推出階段中的新軟體版本。 最好是這個後端架構應該變為透明，API 消費者。 這可以達成類似的方式，為透明的版本設定，因為它所根據的操作使用每個 API 金鑰設定狀態的後端 URL 相同技巧。  

傳回喜好的版本，每個訂閱索引鍵的 api，您將會傳回與租用戶相關的已指派的硬體群組識別碼。 可以使用該識別項，以建構適當的後端 URL。

## <a name="summary"></a>摘要
若要儲存任何類型的資料使用 Azure API 管理快取自由度可讓有效的存取權的影響處理輸入的要求的方式設定資料。 它也可以用來儲存資料片段，可以擴大所傳回的後端 API 的回應。

## <a name="next-steps"></a>後續步驟
請不吝賜教 Disqus 主題往來中此主題如果有其他案例，這些原則已啟用，或如果有案例達到您想要但不是認為目前可能會執行。
