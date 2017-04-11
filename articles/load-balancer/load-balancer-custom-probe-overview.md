<properties
  pageTitle="負載平衡器自訂探查和監視健康狀態 |Microsoft Azure"
  description="瞭解如何使用自訂探查 Azure 負載平衡器監視負載平衡器前的執行個體"
  services="load-balancer"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure-resource-manager"
/>
<tags
  ms.service="load-balancer"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="10/24/2016"
  ms.author="sewhee" />

# <a name="understand-load-balancer-probes"></a>了解負載平衡器探查

Azure 負載平衡器提供功能，可以使用探查監控伺服器執行個體的狀況。 當檢查無法回應時，負載平衡器會停止傳送新連線至不佳的執行個體。 不受影響現有的連線，，新的連線會傳送給健全的執行個體。

雲端服務角色 （工作者角色和 web 角色） 探查監視使用來賓代理程式。 當您使用虛擬機器負載平衡器前，必須先設定 TCP 或 HTTP 自訂探查。

## <a name="understand-probe-count-and-timeout"></a>了解探查計數與逾時

探查行為而定︰

- 允許要標示的執行個體的成功探查數上移。
- 失敗探查導致要標示的執行個體數目，向下。

逾時與頻率的設定值 SuccessFailCount 決定是否要執行或不在執行中確認執行個體。 在 Azure 入口網站，逾時設定為兩個時間值的頻率。

所有的負載平衡執行個體，端點 （也就是負載平衡設定） 探查設定必須相同。 這表示您不能有不同的探查設定為每個角色執行個體或虛擬機器中的特定端點組合相同裝載的服務。 例如，每個執行個體必須擁有相同的本機連接埠和逾時。

>[AZURE.IMPORTANT] 負載平衡器探查所用的 IP 位址 168.63.129.16。 這個公用 IP 位址，協助提到您-擁有-IP Azure 虛擬網路案例的內部的平台資源的通訊。 虛擬的公用 IP 位址 168.63.129.16 用於所有區域，並不會變更。 我們建議您在任何本機防火牆原則允許此 IP 位址。 它不應視為安全性風險因為只內部 Azure 平台可資料來源中的地址的訊息。 如果您不要這麼做，會發生未預期的行為，在許多情況下，例如設定相同的 IP 位址範圍 168.63.129.16 和有重複的 IP 位址。

## <a name="learn-about-the-types-of-probes"></a>深入了解探查的類型

### <a name="guest-agent-probe"></a>來賓代理程式探查

這個探查才可供 Azure 雲端服務。 負載平衡器利用來賓代理程式內虛擬機器，然後接聽並執行個體是在準備好狀態時，才回應 HTTP 200 OK 回應 （也就是不是在另一個狀態忙碌]、 [回收或停止等）。

如需詳細資訊，請參閱[設定服務定義檔案 (csdef) 的狀況檢查](https://msdn.microsoft.com/library/azure/ee758710.aspx)或[開始建立雲端服務的具網際網路負載平衡器](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services)。

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>標示為不佳的執行個體來賓代理程式探查為何？

如果來賓代理程式無法使用 HTTP 200 OK 回覆，負載平衡器標示為沒有回應的執行個體，然後停止傳送流量的執行個體。 負載平衡器會繼續執行個體偵測 （ping）。 如果來賓代理程式回應 HTTP 200，負載平衡器會傳送流量的執行個體一次。

當您使用 web 角色時，網站程式碼通常是執行中 w3wp.exe，不由 Azure 監視布料的轉印圖樣或來賓代理程式。 這代表失敗 w3wp.exe （例如，HTTP 500 回應） 不會報告來賓代理程式，以及負載平衡器不會旋轉的執行個體。

### <a name="http-custom-probe"></a>HTTP 自訂探查

自訂 HTTP 負載平衡器探查會覆寫預設來賓代理程式探查，這表示您可以建立自己的自訂邏輯，以判斷角色執行個體的狀況。 負載平衡器依預設，每 15 秒，將探查您結束點。 執行個體會被視為位於負載平衡器旋轉，如果它會出現逾時期間 （預設 31 秒） 內回應 HTTP 200。

這可能是如果您想要實作您自己的邏輯，若要移除負載平衡器旋轉的執行個體。 例如，您可以決定移除執行個體，如果它是 90 %cpu 上方，並傳回非 200 狀態。 如果您有使用 w3wp.exe 的 web 角色，這也表示您收到自動監控您的網站，因為您網站的程式碼中的錯誤會傳回負載平衡器探查非 200 狀態。

>[AZURE.NOTE] HTTP 自訂探查支援相對路徑和只 HTTP 通訊協定。 不支援 HTTPS。

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>標示為不佳的執行個體 HTTP 自訂探查為何？

- HTTP 應用程式會傳回 HTTP 回應碼 200 （例如，403、 404 或 500） 以外。 這是應用程式執行個體應該採取登出服務立即正通知。
- 完全逾時期間之後 HTTP 伺服器無回應。 根據逾時值設，這可能表示的多個探查要求移未接聽之前探查會標示為未執行 （亦即之前 SuccessFailCount 探查傳送）。
- 伺服器關閉透過 TCP 重設的連線。

### <a name="tcp-custom-probe"></a>TCP 自訂探查

TCP 探查執行定義的連接埠與三種方式交換起始連線。

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>標示為不健全的執行個體的 TCP 自訂的檢查為何？

- TCP 伺服器沒有完全回應逾時期間之後。 當探查被標示為未執行取決於已設定到未回答之前標示為未執行探查失敗的探查要求的數目。
- 探查接收 TCP，重設的角色執行個體。

如需有關如何設定 HTTP 狀況探查或 TCP 檢查的詳細資訊，請參閱[開始建立具網際網路負載平衡器資源管理員] 中使用 PowerShell](load-balancer-get-started-internet-arm-ps.md#create-lb-rules-nat-rules-a-probe-and-a-load-balancer)。

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>新增回負載平衡器旋轉健全的執行個體

TCP 及 HTTP 探查視為健全，且標示為良好的時間的角色執行個體︰

- 負載平衡器取得正檢查第一次 VM 開機。
- 數字的 SuccessFailCount （稍早所述） 定義的成功探查標示為健全的角色執行個體所需的值。 如果已移除角色執行個體，成功、 連續探查數就必須等於或大於 SuccessFailCount 標記為執行該角色執行個體的值。

>[AZURE.NOTE] 如果角色執行個體的狀況是變動、 負載平衡器等待再加回健全狀態的角色執行個體。 這是透過原則以保護使用者和基礎結構。

## <a name="use-log-analytics-for-load-balancer"></a>使用記錄分析負載平衡器

若要查看 [探查健康狀態] 和 [探查計算，您可以使用[記錄負載平衡分析資料](load-balancer-monitor-log.md)。 記錄可以用於 Power BI 的 Azure 操作深入資訊提供狀況的負載平衡器的狀態相關統計資料。
