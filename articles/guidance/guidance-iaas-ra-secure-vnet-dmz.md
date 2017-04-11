<properties
   pageTitle="Azure 架構參考 IaaS︰ 實作 Azure 與網際網路之間 DMZ |Microsoft Azure"
   description="如何 Azure 中實作安全的混合式網路架構與存取網際網路。"
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>實作 Azure 與網際網路之間 DMZ

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

本文將說明實作安全的混合式網路延伸您接受 Azure 從網際網路網路流量的內部網路的最佳作法。 此參考架構延伸[實作 DMZ Azure 及內部部署資料中心之間]的文件中所描述架構[implementing-a-secure-hybrid-network-architecture]。 建議您閱讀文件，並瞭解參考架構之前先閱讀這份文件。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰[資源管理員][resource-manager-overview]和標準。 此參考架構會使用資源管理員，Microsoft 建議新的部署。 

此結構的一般的使用案例包括︰

- 混合式應用程式位置負載執行只能內部部署和只能在 Azure。

- Azure 基礎結構路由從內部部署和網際網路的內送流量。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示在這種架構的重要元件︰

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是 「 DMZ-公用 」 頁面上。

[![0]][0]

- **公用 IP 位址 (PIP)。** 這是公用端點的 IP 位址。 連線到網際網路的外部使用者可以存取系統透過此位址。

- **網路虛擬應用裝置 (NVA)。**  NVA 是描述 VM 執行工作，例如允許或拒絕存取為防火牆、 最佳化 WAN 作業 （包括網路壓縮）、 自訂路由]，或其他網路功能的一般術語。

- **Azure 負載平衡器。** 從網際網路所有傳入的邀請通過此負載平衡器和分配給 NVAs 公用 DMZ 中輸入子網路。

- **公用 DMZ 連入子網路。** 子網路接受要求 Azure 負載平衡器。 傳入的邀請會傳送到其中一個在 DMZ NVAs。

- **公用 DMZ 輸出子網路。** 子網路內部的負載平衡器 web 層通過 NVA 認可的要求。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇，建立您自己參考架構，您應該遵循上述建議，除非您有建議不符合特定需求。

### <a name="nva-recommendations"></a>NVA 建議

實作 NVAs 流量來自網際網路，另一個用於流量來自內部部署的一組。 是只有一組 NVAs 使用兩個，因為此設計提供兩組網路流量之間沒有安全性外的圍安全性風險。 它會使用這項設計，因為它的複雜度檢查安全性規則的優勢，並讓您更清楚的規則會對應至每個傳入的網路要求。 例如，一組 NVAs 實作只在另一組只有內部部署流量 NVAs 實作規則時的網際網路流量的規則。

包含 [圖層 7 NVA 終止 NVA 層級的應用程式連線及維護作業以後端層的相關性。 這可以確保對稱的後端層回應流量傳回透過 NVA 的連線。  

### <a name="public-load-balancer-recommendations"></a>公用負載平衡器建議 ###

若要維持延展性及可用性，部署公用 DMZ[可用性設定]中輸入 NVAs[availability-set]並使用[網際網路對負載平衡器][load-balancer]上可用性集中 NVAs 散佈網際網路要求。  

設定為接受要求僅在必要的網際網路流量的連接埠負載平衡器。 例如，限制輸入的 HTTP 要求連接埠 80 和連接埠 443 的輸入的 HTTPS 要求。

## <a name="scalability-considerations"></a>延展性考量

使用網際網路圖示的負載平衡器前面輸入公用 DMZ 子網路從開始設計您的基礎結構。 即使您架構 initally 需要單一 NVA，它會調整為多個 NVAs 未來網際網路對負載平衡器已經部署變得更容易。

## <a name="availability-considerations"></a>可用性考量

對負載平衡器網際網路需要每個 NVA 公用 DMZ 中的輸入子網路實作[狀況探查][lb-probe]。 無法在此端點回應的狀況檢查被視為無法使用，而且負載平衡器會要求導向到其他 NVAs 在相同的可用性設定。 請注意，如果所有 NVAs 都無法回應，您的應用程式將會都失敗，請務必有監控設定提醒 DevOps 當健全 NVA 執行個體數目低於臨界值。

## <a name="manageability-considerations"></a>管理能力考量

限制輸入公用 DMZ NVA 的回應要求從只管理子網路中的 [跳轉] 方塊的監控和管理功能。 如所述的[實作 Azure 及內部部署資料中心之間 DMZ] [implementing-a-secure-hybrid-network-architecture]的文件、 管理子網路限制存取中定義單一網路路由從內部部署網路透過閘道器至 [捷徑] 方塊。

如果從您的內部部署網路 Azure 的閘道器連線中斷，您仍部署 PIP，從網際網路新增跳轉] 方塊中，並在遠端連絡跳轉] 方塊。

## <a name="security-considerations"></a>安全性考量

此參考架構實作多個安全性層級︰

- 網際網路對負載平衡器會指示 NVAs 要求輸入公用 DMZ 子網路中，並僅在應用程式的連接埠。

- 輸入與輸出公用 DMZ 子網路的 NSG 規則防止 NVAs 遭到封鎖外部 NSG 規則的要求。

- NAT 路由設定 NVAs 的指示連接埠 80 和連接埠 443 的網頁層負載平衡器傳入的邀請，但會忽略要求所有其他的連接埠。

請注意，您應該記錄所有傳入的邀請上所有的連接埠。 定期稽核記錄中，注意外預期的參數，因為這些可能表示入侵嘗試的要求。

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>封鎖/行程流量應用程式層之間使用 NSGs

每個網頁、 商業及資料層限制它們之間的資料傳輸使用 NSGs。 也就是商務層使用 NSG 封鎖所有流量的不是在網頁層，且資料層使用 NSG 封鎖所有不是在商務層的流量。 如果您有以展開 NSG 規則，允許更廣泛存取這些層需求，權衡這些需求，針對安全性風險。 每個新的連入的路徑代表意外或故意資料外洩或應用程式損毀的商機。

## <a name="solution-deployment"></a>解決方案部署

實作這些建議參照結構的部署會提供 Github。 此參考架構包括虛擬網路 (VNet)、 網路安全性群組 (NSG)、 負載平衡器，以及兩個虛擬機器 (Vm)。

參考架構可以使用 Windows 或 Linux Vm 部署按照下列指示︰ 

1. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. 一旦連結已開啟 Azure 入口網站中，您必須輸入值的部分設定︰ 
    - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-public-dmz-network-rg`在文字方塊中。
    - 從 [**位置**] 下拉式方塊中選取的地區。
    - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
    - 選取的下拉式方塊、 **windows**或**linux**的**Os 類型**。
    - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
    - 按一下 [**購買**] 按鈕。

3. 等到完成部署。

4. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. 一旦連結已開啟 Azure 入口網站中，您必須輸入值的部分設定︰ 
    - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-public-dmz-wl-rg`在文字方塊中。
    - 從 [**位置**] 下拉式方塊中選取的地區。
    - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
    - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
    - 按一下 [**購買**] 按鈕。

6. 等到完成部署。

7. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. 一旦連結已開啟 Azure 入口網站中，您必須輸入值的部分設定︰ 
    - **資源群組**名稱已經定義參數檔案中，選取 [**使用現有**，然後輸入`ra-public-dmz-network-rg`在文字方塊中。
    - 從 [**位置**] 下拉式方塊中選取的地區。
    - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
    - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
    - 按一下 [**購買**] 按鈕。

9. 等到完成部署。

10. 參數檔案包含的所有 Vm 的硬式編碼的系統管理員使用者名稱和密碼，然後強烈建議您立即變更兩者]。 針對每個 VM 部署中，在 Azure 入口網站中加以選取，然後按一下**重設密碼****支援 + 疑難排解**刀中。 在 [**模式**] 下拉式清單方塊中，選取 [**重設密碼**，然後選取新的**使用者名稱**和**密碼**。 按一下 [**更新**] 按鈕，若要保存。


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "安全的混合式網路架構"
