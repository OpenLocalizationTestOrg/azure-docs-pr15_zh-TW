<properties
   pageTitle="在高可用性部署虛擬就 |Microsoft Azure"
   description="如何在高可用性部署網路虛擬應用裝置。"
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>部署虛擬就高可用性

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文概述一組的作法部署網路虛擬應用裝置 (NVAs) 高度可用的方式。 開始之前，請確定您瞭解如何[使用者定義的路徑 (UDR)] [udr-overview]和[負載平衡器][ lb-overview] Azure 中的工作。

您可以使用在 Azure marketplace 的不同 NVAs 可用來擴充的 Azure 功能以相同的方式，您可以使用就在您的內部部署資料中心。 下圖顯示的[單一 NVA]範例部署[nva-scenario]為防火牆應用裝置。 

![[0]][0]

雖然上述部署運作，但它會介紹單一的失敗。 如果虛擬應用裝置失敗，請沒有流量流向。 若要解決這個問題，您需要使用多個 NVAs。 不過，也需要根據您的需求使用其他設定及資源。

您可以使用下列解決方案的其中一個部署高度可用的 NVA 環境。

|解決方案|優點|考量|
|---|---|---|
|輸入圖層 7 虛擬就使用|所有的節點是作用中|需要，可終止連線用 SNAT NVA<br/>需要從網際網路]，並從 Azure 流量 NVAs 個別設定<br/>僅可用於起始外 Azure 流量|
|輸入-出口與圖層 7 虛擬裝置|所有的節點是作用中<br/>可以處理來自於 Azure 流量 |需要，可終止連線用 SNAT NVA<br/>需要從網際網路]，並從 Azure 流量 NVAs 個別設定|
|PIP UDR 切換|所有流量的一組 NVAs<br/>可以處理所有流量 （連接埠規則沒有限制）|主動被動<br/>需要容錯移轉程序|

## <a name="ingress-with-layer-7-virtual-appliances"></a>輸入圖層 7 虛擬就使用
您可以使用一組 NVAs 前 Azure 負載平衡器提供連線至 Azure 負載前一伺服器端連接埠 （例如 HTTP 和 HTTPS）。 下圖會醒目提示如何提供在這個案例中 NVA 層級的可用性。

![[1]][1]

在此案例中，必須結束所有連線，網路虛擬應用裝置使用，並將其傳遞給工作負載子網路中。 工作負載的虛擬機器 (Vm) 回應 NVA 收到要求，並沒有問題的流量。 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>輸入-出口與圖層 7 虛擬裝置
您可以延伸上述架構，並新增另一組 NVAs 處理流量來自 Azure 由 NVAs，來處理，如下圖所示︰

![[2]][2]

在此案例中，針對 Azure 中的所有流量會都路由至分配之間不同的 NVAs 集的載入內部負載平衡器。 這些 NVAs 直接使用其個別的公用 IP 位址網際網路流量。 

## <a name="pip-udr-switch"></a>PIP UDR 切換
您可以在避免主動被動模式中使用兩個 NVAs 建立多個 NVA 堆疊。 在此案例中，您可以切換的公用 IP 位址 (PIP) 及使用者定義的路徑 (UDRs) 時使用中的節點停駐點。  

![[3]][3]

這種情況非常類似單一 NVA 案例。 唯一的差別必須變更 PIP 和 UDRs 切換 NVAs 之間的流量。 可以手動進行這些變更，或者您也可以將它們。 若要自動化，您可以部署至這兩個 NVAs 的作用中的節點的狀況檢查有應用程式。 向下作用中的節點後，您的應用程式可以變更 PIP 和 UDRs 連結至被動式節點。

此方案的可能實作是使用[動物園管理員][zookeeper]上處理 （您判斷哪一個為作用中的節點） 的前置字元選舉 NVAs 精靈。 後就是使用前置字元，它呼叫 Azure REST api PIP 移除失敗的節點，並將其附加至前置字元。 它也應該變更 UDRs 指向新的前置字元私用的 IP 位址。

## <a name="next-steps"></a>後續步驟

- 瞭解如何[實作 Azure 及內部部署資料中心之間 DMZ] [dmz-on-prem]使用圖層 7 NVAs。
- 瞭解如何[實作 Azure 與網際網路之間 DMZ] [dmz-internet]使用圖層 7 NVAs。

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "單一 NVA 架構"
[1]: ./media/guidance-nva-ha/l7-ingress.png "輸入圖層 7"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "7 輸入與輸出的 [圖層"
[3]: ./media/guidance-nva-ha/active-passive.png "主動被動叢集"