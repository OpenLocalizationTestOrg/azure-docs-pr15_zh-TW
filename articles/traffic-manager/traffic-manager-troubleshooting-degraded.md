<properties
    pageTitle="疑難排解降低上 Azure 流量管理員狀態"
    description="如何疑難排解流量管理員設定檔時，會顯示為降低狀態。"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>疑難排解降低上 Azure 流量管理員狀態

本文將說明如何疑難排解 Azure 流量管理員設定檔顯示降級的狀態。 這個案例中，請考慮您已設定傳輸管理員設定檔指向部分 cloudapp.net 代管服務。 當您核取流量主管的狀況時，您會看到 [狀態服務已降級。

![降級的狀態](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

如果您移到該設定檔中的 [結束點] 索引標籤，您會看到一或多個以離線狀態的結束點︰

![離線](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>了解流量管理員探查

- 流量管理員會考慮要 ONLINE 探查探查路徑中回接收 HTTP 200 回應時，才端點。 任何其他非 200 回應是失敗。
- 30 x 重新導向會失敗，即使重新導向的 URL 會傳回 200。
- HTTPs 探查會忽略憑證錯誤。
- 探查路徑的實際內容不會影響，只要 200 會傳回。 探查 URL 一些靜態內容，例如 「 / favicon.ico 」 是常見的方法。 動態內容，例如 ASP 頁面中，不一定會傳回 200，即使健全應用程式。
- 最佳作法是探查將路徑] 設定有足夠的邏輯，以判斷，網站的向上或向下的項目。 在上一個範例中，設定為 [路徑 」 / favicon.ico 」，您只是測試該 w3wp.exe 回應。 這個探查可能不會指出您的 web 應用程式狀況良好。 建議您選擇就會設定路徑內容，例如 「 / Probe.aspx 」 具有邏輯，以判斷網站的健全狀況。 例如，您無法使用的 CPU 使用率效能計數器或測量失敗要求的數目。 或者您可以嘗試存取資料庫資源或工作階段的狀態，以確保 web 應用程式正常運作。
- 如果所有的設定檔中的端點會降低，然後流量管理員會將所有以良好的結束點和路由流量至所有的端點。 這種行為可確保您的問題探查機制不會導致您的服務的完整資料。

## <a name="troubleshooting"></a>疑難排解

若要疑難排解這個錯誤，您需要的工具，以顯示 HTTP 狀態碼傳回探查 URL。 有許多的工具，顯示您的原始 HTTP 回應。

* [Fiddler](http://www.telerik.com/fiddler)
* [捲曲](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

此外，您可以使用 Internet Explorer 中的 F12 偵錯工具 [網路] 索引標籤，來檢視 HTTP 回應。

我們想要查看我們的探查 URL 的回應此範例︰ http://watestsdp2008r2.cloudapp.net:80/探查。 下列 PowerShell 範例說明此問題。

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

輸出範例︰

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

請注意，我們會收到重新導向回應。 如先前所述，任何 StatusCode 以外 200 會被視為失敗。 流量管理員端點狀態變更為離線。 若要解決此問題，請核取的網站 」 設定，以確保可以傳回從探查路徑的適當 StatusCode。 重新設定流量管理員探查指向傳回 200 的路徑。

如果您探查使用 HTTPS 通訊協定，您可能需要停用來測試期間避免 SSL/TLS 錯誤檢查的憑證。 下列 PowerShell 陳述式停用憑證驗證，在目前的 PowerShell 工作階段︰

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>後續步驟

[關於流量管理員流量路由方法](traffic-manager-routing-methods.md)

[什麼是流量管理員](traffic-manager-overview.md)

[雲端服務](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web 應用程式](https://azure.microsoft.com/documentation/services/app-service/web/)

[作業上流量管理員 （REST API 參照）](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure 流量管理員 Cmdlet][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
