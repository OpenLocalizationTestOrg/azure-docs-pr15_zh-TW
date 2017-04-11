

<properties
   pageTitle="監控概觀 Azure 應用程式的閘道器的狀況 |Microsoft Azure"
   description="深入了解 Azure 應用程式閘道監控功能"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="application-gateway-health-monitoring-overview"></a>應用程式閘道器狀況監控概觀

預設的 azure 應用程式閘道監視其的後端資料庫中的所有資源的狀況，並會自動移除任何資源資料庫中被視為不佳。 應用程式閘道持續監控不佳的執行個體，並將它們新增至健全的後端資料庫後，才可供使用和回應狀況探查。 應用程式閘道傳送探查使用相同的連接埠後端 HTTP 設定中所定義的狀況。 如此一來，可確保探查正在測試相同的連接埠客戶想要用來連線到後端。

![應用程式閘道器探查範例][1]

除了使用預設健康探查監視，您也可以自訂狀況探查，以符合您的應用程式的需求。 本文中，預設和自訂健康探查覆蓋。

## <a name="default-health-probe"></a>預設狀況探查

當您沒有設定任何自訂探查設定時，應用程式閘道器會自動設定預設狀況檢查。 監控行為的運作方式設定後端資料庫的 IP 位址進行 HTTP 要求。 預設探查如果後端 http 被設定為 HTTPS 探查會使用 https 以及來測試 backends 的狀況。

例如︰ 設定您的應用程式閘道器，使用 A、 B 和 C 的後端伺服器接收連接埠 80 HTTP 網路流量。 預設狀態監控測試三個伺服器健全的 HTTP 回應每 30 秒。 狀況良好的 HTTP 回應有[狀態碼](https://msdn.microsoft.com/library/aa287675.aspx)介於 200 和 399 之間。

如果預設探查檢查失敗的伺服器，請應用程式閘道器會將其移除其後端資料庫，然後網路資料傳輸停止傳送此伺服器。 預設探查仍會繼續檢查伺服器每隔 30 秒。 伺服器的回應時成功一個要求從預設的狀況檢查，新增回狀況良好到後端資料庫中，並再次傳送至伺服器的流量開始。

### <a name="default-health-probe-settings"></a>預設的狀況檢查設定

|這個屬性 | 值 | 描述|
|---|---|---|
| 這個 URL| http://127.0.0.1:\<連接埠\>/ | URL 路徑 |
| 間隔 | 30 | 探查間隔秒數 |
| 逾時  | 30 | 秒數探查逾時 |
| 不佳的閥值 | 3 | 檢查重試計數。 後端伺服器是之後標示為 [向下探查連續失敗次數達到不佳的閥值。 |

> [AZURE.NOTE] 連接埠一律為後端 HTTP 設定為相同的連接埠。

預設探查看起來就只會在 http://127.0.0.1:\<連接埠\>決定健康狀態。 如果您需要設定狀況探查，若要移至 [自訂 URL 或修改的任何其他設定，您必須使用自訂的檢查，如以下步驟所述。

## <a name="custom-health-probe"></a>自訂健康探查

自訂探查可讓您微調控制健康監視。 使用自訂的檢查，您可以設定探查間隔、 URL 及路徑，若要測試，以及多少失敗的回覆標示為不佳的後端資料庫執行個體之前，可接受。

### <a name="custom-health-probe-settings"></a>自訂的狀況檢查設定

下表提供定義自訂的狀況檢查的屬性。

|這個屬性| 描述|
|---|---|
| 名稱 | 探查的名稱。 若要參照的後端 HTTP 設定探查會使用此名稱。 |
| 通訊協定 | 用來傳送探查通訊協定。 探查會使用的後端 HTTP 設定中所定義的通訊協定 |
| Host （主機) |  若要傳送探查主機名稱。 適用只有在多個網站上設定應用程式的閘道器，否則使用 '127.0.0.1'。 這是不同的 VM 主機名稱。 |
| 路徑 | 探查相對路徑。 有效的路徑開頭是從第 '/'。 |
| 間隔 | 檢查間隔秒數。 這是兩個連續探查之間的時間間隔。|
| 逾時 | 檢查逾時秒數。 探查標示為失敗如果有效的回應不會收到此逾時期間內。 |
| 不佳的閥值 | 檢查重試計數。 後端伺服器是之後標示為 [向下探查連續失敗次數到達不良臨界值。 |

> [AZURE.IMPORTANT] 如果應用程式的閘道器設定一個網站，預設主機名稱應指定為 '127.0.0.1'，否則在中設定自訂探查除非。
參照自訂探查會傳送至\<通訊協定\>://\<主機\>:\<連接埠\>\<路徑\>。 使用的連接埠都相同的連接埠後端 HTTP 設定中所定義。

## <a name="next-steps"></a>後續步驟

之後學習應用程式的閘道器狀態監控，您可以設定[自訂健康探查](application-gateway-create-probe-portal.md)Azure 入口網站或使用 PowerShell 和 Azure 資源管理員部署模型[自訂健康探查](application-gateway-create-probe-ps.md)中。

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png