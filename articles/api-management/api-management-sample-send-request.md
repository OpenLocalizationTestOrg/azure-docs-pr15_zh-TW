<properties
    pageTitle="使用產生 HTTP 要求的 API 管理服務"
    description="瞭解如何使用 API 管理中的邀請及回應的原則，從您的 API 呼叫外部服務"
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


# <a name="using-external-services-from-the-azure-api-management-service"></a>使用 [從 Azure API 管理服務的外部服務

用於 Azure API 管理服務的原則可以執行各種工作完全根據收到的要求、 傳出回應和基本的設定資訊。 不過，也可以從 API 管理外部服務進行互動原則開啟更多的機會。

我們先前已經看到如何進行互動[Azure 事件中樞的記錄、 監控及服務分析](api-management-log-to-eventhub-sample.md)。 在本文中，我們會示範原則，可讓您與任何外部 HTTP 互動基礎服務。 這些原則可供觸發遠端事件或擷取資訊會用來操作原始邀請及回應方式。

## <a name="send-one-way-request"></a>傳送一種方式-要求
可能的最簡單的外部互動是 fire 並忘記的樣式可讓某些類型的重要事件時收到通知的外部服務的要求。 我們可以使用控制項流程原則`choose`偵測任何一種我們感興趣，然後，如果滿足如下的條件，我們可以使外部 HTTP 要求使用的[傳送一種方式-要求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest)原則的條件。 這可能是郵件系統，例如 Hipchat 或可寬延時間，例如 SendGrid 或 MailChimp，郵件 API 的要求，或類似 PagerDuty 的要徑支援事件。 所有這些郵件系統有簡單 HTTP Api，我們可以輕鬆地叫用。

### <a name="alerting-with-slack"></a>警示與可寬延時間
下列範例會示範如何傳送訊息給可寬延時間的聊天室，HTTP 回應狀態碼是否大於或等於 500。 500 範圍錯誤指出我們 API 的用戶端無法自行解決我們後端 API 問題。 還是需要某種我們的操作。  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

可寬延時間已輸入的網頁連結的概念。 在設定連入的 web 鉤時，可寬延時間會產生特殊的 URL 可讓您執行簡單的文章要求，並將郵件傳送到可寬延時間的頻道。 我們建立 JSON 本文會根據可寬延時間所定義的格式。

![可寬延時間 Web 鉤](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>火並夠忘了嗎？
有某些必須做的取捨時使用的要求 fire 並忘記樣式。 如果基於某種原因，要求失敗，然後將不會報告失敗]。 在此特定的情況下，不保證複雜度次要錯誤報告系統，並等待回應的額外的效能成本。 情況下是基本檢查回應，然後[傳送邀請](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)原則是較佳的選項。

## <a name="send-request"></a>傳送邀請
`send-request`原則可讓使用執行複雜的處理函數，傳回資料 API 管理服務的外部服務可供進一步原則處理。

### <a name="authorizing-reference-tokens"></a>授權參照權杖
主要函數的 API 管理所保護後端資源。 如果您的 API 使用的授權伺服器上建立[JWT 權杖](http://jwt.io/)其 OAuth2 流量，屬於[Azure Active Directory](../active-directory/active-directory-aadconnect.md)一樣，然後您可以使用`validate-jwt`原則，驗證的權杖有效性。 不過，某些授權伺服器建立所謂的[參考權杖](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/)無法驗證不撥打電話至授權伺服器。

### <a name="standardized-introspection"></a>標準化的自我
過去已經標準化的無法驗證授權伺服器的參照權杖。 不過，定義如何資源伺服器可以驗證的權杖 IETF 發佈最近建議標準[RFC 7662](https://tools.ietf.org/html/rfc7662) 。

### <a name="extracting-the-token"></a>擷取權杖
第一步是從授權標頭擷取權杖。 [頁首] 的值應該用來格式化`Bearer`授權配置、 單一的空格，然後依照[RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1)的授權 token。 很抱歉一些情況下授權配置被省略了的位置。 若要剖析時，此帳戶，我們分割標頭值上一個空格，然後選取傳回陣列的字串中的最後一個字串。 這會提供格式的授權標題的因應措施。

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>驗證要求
一旦授權權杖，我們可以進行驗證的權杖的要求。 RFC 7662 呼叫此程序自我，並會要求您`POST`自我資源的 HTML 表單。 HTML 表單必須至少包含與索引鍵的金鑰/值組`token`。 這項要求授權伺服器也必須以確保惡意用戶端無法移 trawling 有效的權杖經過驗證。

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>檢查回應
`response-variable-name`屬性用來傳回的回應授與存取權。 此屬性中定義的名稱可將鍵`context.Variables`存取字典`IResponse`物件。

從回應物件中，我們可以擷取本文及 RFC 7622 告訴我們回應必須 JSON 物件，而且必須包含至少屬性稱為`active`就是的則為 true 的值。 當`active`為 true，然後權杖會被視為有效。

### <a name="reporting-failure"></a>報告失敗
我們使用`<choose>`原則偵測權杖無效時，如果是這樣，傳回 401 回應。

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

依照[RFC 6750](https://tools.ietf.org/html/rfc6750#section-3)說明如何`bearer`應使用權杖，我們也會傳回`WWW-Authenticate`401 回應標頭。 WWW 驗證被用來指示的用戶端如何建構適當授權的要求。 各種不同的方法可能與 OAuth2 架構，因為很難通訊所有需要的資訊。 所幸有進行為了協助[用戶端探索如何正確授權資源伺服器的要求](http://tools.ietf.org/html/draft-jones-oauth-discovery-00)。

### <a name="final-solution"></a>最終解決方案
將所有片段都放在一起，我們會收到下列原則︰

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

這是其中一個許多範例如何`send-request`原則可以用來將實用的外部服務整合到邀請和回應通過 API 管理服務的程序。

## <a name="response-composition"></a>回應撰寫
`send-request`原則可供您在上一個範例中，我們看到或作為後端通話的完整取代加強後端系統的主要要求。 使用此技巧我們可以輕鬆地建立複合索引彙總的資源從多個不同的系統。

### <a name="building-a-dashboard"></a>建立儀表板   
有時候您要能夠公開，例如存在於多個後端系統中的資訊，而驅動儀表板。 Kpi 來自所有不同後端，但您不想對其直接存取，就是很好如果無法擷取單一邀請中的所有資訊。 可能的一些後端資訊需要一些切割和 dicing 那進行安全性過濾第一次 ！ 能夠快取複合資源可能有助於減少的後端載入，當您知道使用者擁有習慣才能如果其 underperforming 指標可能會變更，請參閱 hammering F5 鍵。    

### <a name="faking-the-resource"></a>假裝資源
建置我們儀表板資源的第一個步驟是 API 管理 publisher 入口網站中設定新的作業。 這是用來設定來建立我們動態資源我們撰寫原則的版面配置區作業。

![儀表板作業](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>提出的要求
一次`dashboard`已建立作業我們可以設定該作業特別的原則。 

![儀表板作業](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

第一步是從傳入的邀請，擷取任何查詢參數，讓我們可以將它們轉寄給我們的後端。 在此範例中，我們儀表板有顯示根據一段時間的資訊因此`fromDate`和`toDate`參數。 我們可以使用`set-variable`原則，從要求 URL 擷取的資訊。

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

我們備有這項資訊後我們可以讓所有的後端系統要求。 每個要求建構新的 URL，以參數資訊通話其伺服器，並將回應儲存在內容變數。

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

不理想的順序就會執行這些要求。 即將來臨的版本中我們將會介紹新原則稱為`wait`的會讓所有的同時執行這些要求。

### <a name="responding"></a>回應

若要建立複合索引的回應，我們可以使用[傳回回應](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse)原則。 `set-body`項目可以使用運算式來建構新`JObject`與內嵌為內容中的元件表示。

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

完成的原則如下所示︰

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

設定中的 [版面配置區作業我們可以設定儀表板資源，因為我們瞭解性質的資料快取至少有一個小時表示，即使是小時過期，仍會充分有效來傳達給使用者的重要資訊。

## <a name="summary"></a>摘要
Azure API 管理服務提供非常有彈性可以選擇性地套用至 HTTP 流量的原則，並讓後端服務的組合。 您是否要強化您 API 閘道警示函數、 驗證、 驗證功能，或建立新的複合資源根據多個後端服務，`send-request`相關的原則開啟明亮的。

## <a name="watch-a-video-overview-of-these-policies"></a>觀看這些原則的概觀
如需有關[傳送一種方式-要求](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest)[傳送邀請](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)，與本文所述的[傳回回應](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse)原則的詳細資訊，請觀看以下影片。

> [AZURE.VIDEO send-request-and-return-response-policies]
