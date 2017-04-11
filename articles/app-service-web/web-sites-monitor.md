<properties
    pageTitle="監控 Azure 應用程式服務中的應用程式"
    description="瞭解如何使用 Azure 入口網站中監控 Azure 應用程式服務中的應用程式。"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="byvinyal"/>

# <a name="how-to-monitor-apps-in-azure-app-service"></a>如何︰ 監控 Azure 應用程式服務中的應用程式

[應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)，提供[Azure 入口網站](https://portal.azure.com)中的內建的監控功能。
這包含檢閱**配額**和**指標**的應用程式，以及設定**提醒**及偶數**縮放**自動根據下列指標的應用程式服務計劃的能力。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>了解配額和指標

### <a name="quotas"></a>配額

裝載於應用程式服務的應用程式與主旨，他們可以使用的資源的特定*限制*。 限制是由與應用程式相關聯的**應用程式服務方案**所定義。

如果應用程式裝載**免費**或**共用**計劃中，然後在 [應用程式可以使用的資源限制是由**配額**定義。

如果應用程式裝載在**基本**、**標準**或**進階版**方案，然後按下限制在他們可以使用的資源設定**大小**（小型、 中型、 大型） 並**執行個體計數**（1、 2、 3，...） 的**應用程式服務方案**。

**配額**的**免費**或**共用**應用程式是︰

* **CPU(Short)**
   * 允許此應用程式中 3 分鐘的時間間隔的 CPU 量。 此配額重新設定每 3 分鐘。
* **CPU(Day)**
   * 允許此應用程式中某一天的 CPU 總數。 此配額重新設定午夜 UTC 每 24 小時。
* **記憶體**
   * 允許此應用程式的記憶體總數。
* **頻寬**
   * 允許此應用程式中某一天的外寄頻寬總數。
   此配額重新設定午夜 UTC 每 24 小時。
* **檔案系統**
   * 允許的儲存空間的總金額。

只適用於裝載於**基本**、**標準**和**進階版**方案的應用程式的配額是**檔案系統**。

深入瞭解特定配額限制，可使用不同的應用程式服務 Sku 功能可以在這裡找到︰ [Azure 訂閱服務限制](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>配額強制

如果應用程式中使用超過**CPU （簡稱）**， **[CPU （天）**或**頻寬**配額的應用程式將會停止直到配額重新設定。 在這段時間，所有傳入的邀請就會導致**HTTP 403**。
![][http403]

如果超過應用程式**的記憶體**配額時，應用程式將會重新啟動。

如果**檔案系統**已超出配額，然後任何撰寫作業會失敗，包括寫入記錄檔。

配額可提高或移除您的應用程式從升級您的應用程式服務方案。

### <a name="metrics"></a>指標

**指標**會提供應用程式或應用程式服務計劃的行為的相關資訊。

**應用程式**中，可使用指標是︰

* **平均回應時間**
   * 應用程式的服務要求，然後在 [ms 平均時間。
* **平均記憶體工作集**
   * Mib 使用應用程式中的記憶體平均數量。
* **CPU 時間**
   * 應用程式所使用的秒數 CPU 量。 如需此單位，請參閱︰ [CPU 時間與 CPU 百分比](#cpu-time-vs-cpu-percentage)
* **中的資料**
   * 使用應用程式中 Mib 的內送頻寬量。
* **查看的資料**
   * 使用應用程式中 Mib 的外寄頻寬量。
* **Http 2xx**
   * 計算的結果 http 狀態碼的要求 > = 200 但 < 300。
* **Http 3xx**
   * 計算的結果 http 狀態碼的要求 > = 300 但 < 400。
* **Http 401**
   * 要求 HTTP 401 狀態碼結果的計數。
* **Http 403**
   * 要求結果 HTTP 403 狀態碼的計數。
* **Http 404**
   * 要求結果 HTTP 404 狀態碼的計數。
* **Http 406**
   * 要求 HTTP 406 狀態碼結果的計數。
* **Http 4xx**
   * 計算的結果 http 狀態碼要求 > = 400 但 < 500。
* **Http 伺服器錯誤**
   * 計算的結果 http 狀態碼要求 > = 500 但 < 600。
* **記憶體工作集**
   * 目前 Mib 中的應用程式所使用的記憶體量。
* **要求**
   * 無論他們所產生的 HTTP 狀態碼要求的總數。

**應用程式服務計劃**中，可使用指標是︰

>[AZURE.NOTE] 應用程式服務方案計量只適用於**基本**、**標準**和**進階版**SKU 的方案。

* **CPU 百分比**
   * 平均的 CPU 使用計劃中的所有執行個體。
* **記憶體百分比**
   * 使用計劃中的所有執行個體的平均記憶體。
* **中的資料**
   * 使用計劃中的所有執行個體的平均傳入頻寬。
* **查看的資料**
   * 外寄用於規劃的所有執行個體的頻寬平均值。
* **磁碟佇列長度**
   * 平均數目讀取及撰寫上儲存的要求。 高磁碟佇列長度是可能會變慢，因為過度的磁碟 i 的應用程式的指示。
* **Http 佇列長度**
   * 坐在佇列中之前所完成的 HTTP 要求的平均數目。 為高或增加 HTTP 佇列長度是重度負載下的計劃的徵兆。

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 時間與 CPU 百分比
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

有 2 反映 CPU 使用率的指標。 **CPU 時間**和**CPU 百分比**

**CPU 時間**適合用來裝載於**免費**或**共用**方案，因為其配額的其中一個定義 CPU 分鐘應用程式所使用的應用程式。

另一方面**CPU 百分比**是很適合用於應用程式裝載於**基本**、**標準**和**進階版**方案，因為他們可以調整此公制所有執行個體是很好的指標的整體的使用方式。

##<a name="metrics-granularity-and-retention-policy"></a>指標資料粒度和保留原則

登入並彙總的下列資料粒度和保留原則服務應用程式和應用程式服務方案的指標︰

 * 仍會保留**48 小時**的**分鐘**資料粒度指標
 * **30 天**的保留**小時**資料粒度指標
 * 仍會保留**90 天**的**日期**資料粒度指標

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>監視配額和 Azure 入口網站中的指標。

您可以檢視不同的**配額**和**指標**影響[Azure 入口網站](https://portal.azure.com)中的應用程式的狀態。

![][quotas]
可以設定] 下找到**配額**>**配額**。 UX 可讓您檢閱: （1） 的配額名稱、 （2） 其重設間隔、 （3） 其目前的限制和 （4） 目前值。

![][metrics]
**指標**可以直接從資源刀存取。 您也可以自訂圖表: (1)**按一下**，然後選取 [(2)**編輯圖表**。
從這裡開始，您可以變更 (3) 的**時間範圍**、 (4)**圖表類型**和 (5) 若要顯示**指標**。  

您可以進一步瞭解以下指標︰[監視服務指標](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

## <a name="alerts-and-autoscale"></a>通知和自動調整大小
指標最加以連結的應用程式或應用程式服務方案的通知，深入了解，請參閱[收到通知的通知](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

裝載於基本、 標準或付費應用程式服務方案的應用程式服務應用程式支援**自動調整大小**。 這個選項可讓您設定的監視應用程式服務方案指標，可以增加或減少視需要提供其他資源的執行個體計算規則或儲存金錢過度佈建應用程式時。 您可以進一步瞭解自動調整大小︰[小數位數的方式](../monitoring-and-diagnostics/insights-how-to-scale.md)，以下是[Azure 監視器自動調整的最佳作法](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)

>[AZURE.NOTE] 如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png
