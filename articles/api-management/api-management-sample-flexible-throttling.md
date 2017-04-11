<properties
    pageTitle="節流 Azure API 管理的進階的要求"
    description="瞭解如何建立和套用彈性配額] 和 [限制 Azure API 管理原則的工資率。"
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


# <a name="advanced-request-throttling-with-azure-api-management"></a>節流 Azure API 管理的進階的要求

能夠節流傳入的邀請是 Azure API 管理的重要的角色。 [藉由控制要求或要求/資料總量率，API 管理可讓 API 提供者，從濫用保護其 Api，並建立不同的 API 產品層的值。

## <a name="product-based-throttling"></a>根據產品節流
若要到目前為止，工資率節流限於特定產品訂閱 （基本上按鍵），定義 API 管理 publisher 入口網站中已有限的功能。 這是很適合用於 API 提供者，若要套用上的開發人員使用其 API 已註冊的限制，不過，不可以協助，例如中節流 API 的個別使用者。 很可能是針對單一取用的整個配額，然後防止其他客戶的開發人員可以使用應用程式開發人員的應用程式的使用者。 此外，可能會產生大量的要求的多個客戶可能會限制偶爾發生的使用者存取權。

## <a name="custom-key-based-throttling"></a>自訂的金鑰以節流
新的[工資率限制-依-鍵](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey)和[配額的金鑰](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey)原則提供流量控制大幅更有彈性的解決方案。 這些新原則可讓您定義識別按鍵將會用來追蹤流量使用的運算式。 容易使用範例說明這的運作的方式。 

## <a name="ip-address-throttling"></a>IP 位址節流
下列原則限制單一用戶端 IP 位址 10 通話 1000000 呼叫的總計與 10000 kb 的頻寬，每個月的每一分鐘。 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

如果在網際網路上的所有用戶端使用唯一的 IP 位址，這可能是有效的方式的限制由使用者的使用方式。 不過，就有很多個使用者會共用因為其存取 NAT 裝置透過網際網路單一的公用 IP 位址。 雖然這允許未經授權的存取的 Api`IpAddress`可能的最佳選擇。

## <a name="user-identity-throttling"></a>使用者身分識別節流
如果已驗證的使用者，然後根據可唯一識別的資訊可以產生節流鍵使用者。

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

讓我們在此範例中擷取授權標頭，將它轉換成`JWT`物件，然後使用的權杖主旨識別的使用者，並使用該為限制索引鍵的比率。 如果使用者身分識別儲存在`JWT`為其中一個其他然後宣告值無法用於其位置。

## <a name="combined-policies"></a>合併的原則
雖然新節流原則提供更多的控制，比現有節流原則，但仍有值結合兩個功能。 依產品訂閱索引鍵 （[訂閱限制通話工資率](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate)和[設定以訂閱的使用量配額](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)） 節流是啟用的 API monetizing 以充電時上傳] 以使用層級的好方法。 更可由使用者節流粗略的控制項互補並防止降級另一位使用者的行為。 

## <a name="client-driven-throttling"></a>用戶端導向節流
當節流鍵定義使用[原則運算式](https://msdn.microsoft.com/library/azure/dn910913.aspx)時，則選擇如何節流範圍的 API 提供者。 不過，開發人員可能會想要控制如何其評分限制自己的客戶。 這可能會啟用 API 提供者介紹自訂頁首允許通訊 api 金鑰開發人員的用戶端應用程式。

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

這可讓開發人員的用戶端應用程式，以選擇要如何建立限制鍵工資率。 實際上精巧的用戶端開發人員可以建立自己的工資率層的按鍵配置給使用者，然後旋轉金鑰的使用方式。

## <a name="summary"></a>摘要
Azure API 管理提供工資率] 和 [節流保護和將值新增至您的 API 服務的報價。 使用自訂的範圍規則的新節流原則可讓您更粗略的控制原則，則若要啟用您的客戶，來建立更好的應用程式。 本文中的範例會示範使用這些新原則的製造比率限制鍵用戶端 IP 位址、 使用者身分識別，與用戶端產生的值。 然而，有可能會使用這類使用者代理程式、 URL 路徑片段的郵件大小郵件的許多其他部分。

## <a name="next-steps"></a>後續步驟
請不吝賜教 Disqus 主題往來中這個主題。 就是很好以獲得的資訊已在您的案例中的邏輯選擇其他潛在關鍵值。

## <a name="watch-a-video-overview-of-these-policies"></a>觀看這些原則的概觀
如需有關本文中包含的[工資率限制-依-鍵](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey)和[配額的金鑰](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey)原則的詳細資訊，請觀看以下影片。

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
