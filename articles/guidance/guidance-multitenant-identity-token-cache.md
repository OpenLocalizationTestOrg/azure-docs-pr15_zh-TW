<properties
   pageTitle="快取 multitenant 應用程式中的存取權杖 |Microsoft Azure"
   description="快取用於啟動後端 Web API 的存取權杖"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>


# <a name="caching-access-tokens-in-a-multitenant-application"></a>在 multitenant 應用程式中的快取存取權杖

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文是[一系列的一部分]。 此外還有隨附這一系列完成[範例應用程式]。

則較昂貴取得 OAuth 存取權杖，因為其需要 HTTP 要求 token 端點。 因此，最好快取權杖盡可能。 [Azure AD 驗證文件庫][ ADAL] (ADAL) 自動快取權杖從 Azure AD，包括重新整理權杖取得。

ADAL 提供預設權杖快取實作。 不過，此 token 快取適用於原生的用戶端應用程式，而不_是_適當的 web 應用程式︰

-   它是靜態執行個體和不安全執行緒。
-   它不適合大量使用者，因為所有使用者的權杖進入相同的字典。
-   它不能共用伺服器陣列中的網頁伺服器上。

不過，您應該執行自訂的權杖快取衍生自 ADAL`TokenCache`課程但適用於伺服器的環境，並提供需要不同的使用者的權杖間隔離的層級。

`TokenCache`類別儲存權杖，發行者、 資源、 用戶端識別碼和使用者編製索引的字典。 自訂的權杖快取應該寫入支援存放區，例如意指快取這個字典。

在 Tailspin 問卷應用程式中`DistributedTokenCache`類別實作權杖快取。 這個實作使用[IDistributedCache] [distributed-cache]從 ASP.NET 核心 1.0 抽象。 如此一來，任何`IDistributedCache`實作可做為支援存放區。

-   根據預設，[問卷] 應用程式會使用意指快取。
-   單一執行個體網頁伺服器，則無法使用 ASP.NET 核心 1.0[記憶體內快取][in-memory-cache]。 （這也是適合用來在開發本機執行的應用程式。）

> [AZURE.NOTE] 目前意指快取不支援的.NET 核心。

`DistributedTokenCache`將快取資料儲存為市集中的支援金鑰/值組。 支援的存放區保留使用者/用戶端區分大小寫的不同的快取資料索引鍵是使用者識別碼，加上用戶端識別碼。

![權杖快取](media/guidance-multitenant-identity/token-cache.png)

支援的存放區是由使用者分割。 每個 HTTP 要求，該使用者的權杖會讀取支援存放區並載入到`TokenCache`的字典。 如果意指用來做為支援的存放區，伺服器陣列中的每個伺服器執行個體讀取/寫入相同的快取，這種方法可以調整至多個使用者。

## <a name="encrypting-cached-tokens"></a>加密快取的權杖

權杖會是機密資料，因為他們授與存取權的使用者的資源。 （此外，與使用者的密碼，您無法只儲存權杖雜的湊）。因此，這是以保護權杖受到危害關鍵。 意指備份快取受到密碼，但如果某人取得密碼時，他們可能會收到所有快取的存取權杖。 因此，`DistributedTokenCache`加密所有項目會將寫入的支援存放區。 完成加密使用 ASP.NET 核心 1.0[資料保護][ data-protection] Api。

> [AZURE.NOTE] 如果您部署至 Azure 網站，加密金鑰備份到網路儲存而在所有電腦上同步處理 (請參閱[金鑰管理][key-management])。 根據預設，鍵未加密執行中 Azure 網站時，但您可以[啟用加密使用 X.509 憑證][x509-cert-encryption]。


## <a name="distributedtokencache-implementation"></a>DistributedTokenCache 實作

[DistributedTokenCache] [DistributedTokenCache]類別衍生自 ADAL [TokenCache] [tokencache-class]類別。

建構函式，`DistributedTokenCache`類別會建立目前使用者鍵，並從支援存放區載入快取︰

```csharp
public DistributedTokenCache(
    ClaimsPrincipal claimsPrincipal,
    IDistributedCache distributedCache,
    ILoggerFactory loggerFactory,
    IDataProtectionProvider dataProtectionProvider)
    : base()
{
    _claimsPrincipal = claimsPrincipal;
    _cacheKey = BuildCacheKey(_claimsPrincipal);
    _distributedCache = distributedCache;
    _logger = loggerFactory.CreateLogger<DistributedTokenCache>();
    _protector = dataProtectionProvider.CreateProtector(typeof(DistributedTokenCache).FullName);
    AfterAccess = AfterAccessNotification;
    LoadFromCache();
}
```

串連的使用者識別碼及用戶端識別碼建立索引鍵 這些取自在使用者的中找到宣告`ClaimsPrincipal`:

```csharp
private static string BuildCacheKey(ClaimsPrincipal claimsPrincipal)
{
    string clientId = claimsPrincipal.FindFirstValue("aud", true);
    return string.Format(
        "UserId:{0}::ClientId:{1}",
        claimsPrincipal.GetObjectIdentifierValue(),
        clientId);
}
```

從支援存放區，而且來電載入快取資料，請閱讀序列的 blob`TokenCache.Deserialize`將 blob 轉換成快取資料。

```csharp
private void LoadFromCache()
{
    byte[] cacheData = _distributedCache.Get(_cacheKey);
    if (cacheData != null)
    {
        this.Deserialize(_protector.Unprotect(cacheData));
    }
}
```

每當 ADAL 存取快取，它會啟動`AfterAccess`事件。 如果快取資料已變更，`HasStateChanged`屬性為 true。 在此情況下，更新以反映的變更，支援存放區，然後設定`HasStateChanged`為 false。

```csharp
public void AfterAccessNotification(TokenCacheNotificationArgs args)
{
    if (this.HasStateChanged)
    {
        try
        {
            if (this.Count > 0)
            {
                _distributedCache.Set(_cacheKey, _protector.Protect(this.Serialize()));
            }
            else
            {
                // There are no tokens for this user/client, so remove the item from the cache.
                _distributedCache.Remove(_cacheKey);
            }
            this.HasStateChanged = false;
        }
        catch (Exception exp)
        {
            _logger.WriteToCacheFailed(exp);
            throw;
        }
    }
}
```

TokenCache 傳送給其他兩個事件︰

- `BeforeWrite`. 之前 ADAL 寫入快取呼叫。 您可以使用這個實作並行策略
- `BeforeAccess`. 之前 ADAL 讀取快取呼叫。 您可以在這裡載入快取以取得最新版本。

在此例中，我們決定不處理這兩個事件。

- 並行，最後一個撰寫獲勝。 因為權杖儲存獨立的每個使用者 + 的用戶端，所以如果相同的使用者會有兩個同時登入工作階段，只會發生衝突時，[確定]。
- 閱讀，我們會載入上每個要求的快取。 要求是短期存在。 如果快取取得修改的時間下, 一個要求挑選新的值。

## <a name="next-steps"></a>後續步驟

- 閱讀本系列下一個文章︰[為客戶 AD FS multitenant Azure 中的應用程式與聯盟][adfs]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[adfs]: guidance-multitenant-identity-adfs.md
[data-protection]: https://docs.asp.net/en/latest/security/data-protection/index.html
[distributed-cache]: https://docs.asp.net/en/latest/fundamentals/distributed-cache.html
[DistributedTokenCache]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.TokenStorage/DistributedTokenCache.cs
[key-management]: https://docs.asp.net/en/latest/security/data-protection/configuration/default-settings.html
[in-memory-cache]: https://docs.asp.net/en/latest/fundamentals/caching.html
[tokencache-class]: https://msdn.microsoft.com/library/azure/microsoft.identitymodel.clients.activedirectory.tokencache.aspx
[x509-cert-encryption]: https://docs.asp.net/en/latest/security/data-protection/implementation/key-encryption-at-rest.html#x-509-certificate
[一系列的一部分]: guidance-multitenant-identity.md
[範例應用程式]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
