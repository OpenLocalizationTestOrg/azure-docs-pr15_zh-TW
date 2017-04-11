<properties
    pageTitle="如何設定應用程式服務環境 |Microsoft Azure"
    description="設定、 管理和監視的應用程式服務環境"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>


# <a name="configuring-an-app-service-environment"></a>設定應用程式服務環境 #

## <a name="overview"></a>概觀 ##

在高的層級，Azure 應用程式服務環境包含數個主要元件︰

- 在 [應用程式服務環境中執行計算資源代管服務
- 儲存空間
- 資料庫
- Classic(V1) 或資源 Manager(V2) Azure 虛擬網路 (VNet) 
- 子網路裝載的應用程式服務環境服務中執行

### <a name="compute-resources"></a>計算資源

您可以使用的計算資源的四個資源集區。  每個應用程式服務環境 (ASE) 都有一組前端與三個可能的工作者集區。 您不需要使用所有三個工作者集區中，如果您想，您可以只使用一或兩個。

資源資料庫 （前端與同事） 中的主機不會直接存取的租用戶。 您無法使用遠端桌面通訊協定 (RDP) 進行連線、 變更其佈建或做為的管理員。

您可以設定資源集區數量和大小。 在 [ASE，有四個的大小選項，會標示為 [第 4 頁透過 P1。 如需這些大小，以及其價格的詳細資訊，請參閱[應用程式服務價格](../app-service/app-service-value-prop-what-is.md)。
變更數量或大小稱為 「 縮放作業。  一次只能有一個縮放作業進行中。

**最上層]: 結束**︰ 前端是 HTTP/HTTPS 結束點會保留在您 ASE 您應用程式。 您不必執行工作負載的前端。

- ASE 開頭的兩個 P2s，這是滿足開發/測試工作量及低功率生產負載所需。 我們強烈建議 P3s 的中等經常生產工作量。
- 重生產工作量中等，建議您已至少四個 P3s，以確保有足夠前端排程進行的維修作業發生時執行。 維護活動擺一個前端一次。 這會減少整體維護活動期間提供前端容量。
- 您無法立即新增新的前端執行個體。 他們可以採取 2 到 3 小時的時間進行佈建。
- 進一步微調小數位數，您應該監視 CPU 百分比、 記憶體百分比，以及前端集區的使用中要求指標。 如果上述 [70%的 CPU 或記憶體百分比是，執行 P3s 時，新增更多的前端。 如果使用中要求值至 15000 20000 的要求，每前端平均，您應該新增更多的前端。 整體的目標是要保留下列 CPU 和記憶體百分比 70%，計算至 15000 要求，每個最上層] 下方的使用中要求結束當您執行的 P3s。  

**同事**︰ 工作者是實際執行您的應用程式。 當您要縮放設定您的應用程式服務方案時，使用相關聯的工作者集區中的同事設定。

- 您無法立即新增工作人員。 他們可以利用 2 到 3 小時的時間進行佈建，不論多少正在加入]。
- 縮放比例計算資源的任何集區的大小會 2 到 3 小時每個更新的網域。 ASE 有 20 更新的網域。 如果您調整計算工作者集區的大小 10 個執行個體，它可能需要 20 至 30 小時的時間才能完成。
- 如果您變更計算資源所使用的背景工作集區的大小，您會造成低溫開始執行該工作集區中的應用程式。

變更計算資源的大小不在執行任何應用程式的工作者集區最快速的方法是︰

- 縮小執行個體計數為 0。 關於 30 分鐘需要解除您執行個體。
- 選取新的計算大小和執行個體數目。 從這裡開始，請 2 到 3 小時的時間才能完成。

如果您的應用程式需要較大的計算資源大小，您無法利用先前提供的指導方針。 而不必變更裝載那些應用程式的工作者集區的大小，您可以填入與同事所要大小的另一個背景工作集區，而且您的應用程式移至該資料庫。

- 在另一個工作資料庫中建立其他的執行個體，視需要的計算的大小。 這會需要從 2 到 3 小時的時間才能完成。
- 重新指派您裝載需要較大的新設定的工作者集區的應用程式的應用程式服務計劃。 這是應該採取一分鐘之內完成快速作業。  
- 如果您不再需要這些未使用過的執行個體，請調整下第一個背景工作集區。 這項作業需要大約 30 分鐘才能完成。

**自動縮放**︰ 的工具，可協助您管理消耗計算資源的其中一個是自動縮放。 您可以使用自動縮放的前端或背景工作集區。 您可以執行上午項目，例如增加您的執行個體的任何集區類型，並減少其在晚上。 或工作者工作者集區中的數目低於特定閾值時，可能是新增執行個體。

如果您想要設定自動調整大小計算資源集區指標周圍的規則，然後請記住佈建需要的時間。 如需自動縮放應用程式服務環境的詳細資訊，請參閱[如何設定自動調整大小的應用程式服務環境中][ASEAutoscale]。

### <a name="storage"></a>儲存空間

每個 ASE 設定 500 GB 的儲存空間。 跨 ASE 中的所有應用程式會使用此空間。 此儲存空間 ASE 的一部分，而目前無法使用您的儲存空間切換。 如果您對您的虛擬網路路由或安全性進行調整，您必須仍允許存取 Azure 儲存空間，或 ASE 無法運作。

### <a name="database"></a>資料庫

資料庫存放定義環境，以及執行中的應用程式的詳細資訊。 這也是 Azure 保留訂閱的一部分。 它不是您可以直接處理的項目。 如果您對您的虛擬網路路由或安全性進行調整，您必須仍允許存取 SQL Azure-或 ASE 無法運作。

### <a name="network"></a>網路

使用您 ASE VNet 可以是您所做的當您建立 ASE 或者提前您所做。 當您建立子網路 ASE 建立期間時，它會強制位於相同的資源群組作為虛擬網路 ASE。 如果您需要使用不同您 VNet 您 ASE 資源群組，您需要建立使用資源管理員範本您 ASE。

有適用於 ASE 虛擬網路的一些限制︰
 
- 虛擬網路必須地區的虛擬網路。
- 需要有 8 或多個地址的子網路 ASE 部署的位置。
- 子網路用來主控 ASE 之後，您無法變更子網路的地址範圍。 因此，建議您子網路包含至少 64 以納入其間的任何未來 ASE 成長的地址。
- 可以不會有其他子網路但 ASE。

不包含 ASE，[虛擬網路]代管服務像[virtualnetwork]子網路且由使用者控制。  您可以管理透過虛擬網路 UI 或 PowerShell 虛擬網路。  在傳統或資源管理員 VNet，可以部署 ASE。  入口網站和 API 體驗傳統和資源管理員 VNets 之間稍有不同，但 ASE 的經驗。

用來裝載 ASE VNet 可以使用 [私用 RFC1918 IP 位址或可用的公用 IP 位址。  如果您想要使用的不屬於 RFC1918 IP 範圍 （10.0.0.0/8、 172.16.0.0/12 192.168.0.0/16），然後建立 VNet 和子網路，供您 ASE ASE 建立之前所需。

此功能會將 Azure 應用程式服務放入虛擬網路，因為這表示您裝載在您 ASE 的應用程式現在可以存取才會透過 ExpressRoute 或網站的虛擬私人網路 (Vpn) 直接的資源。 在您的應用程式服務環境中的應用程式不需要存取裝載您的應用程式服務環境虛擬網路資源其他功能。 這表示您不需要使用 VNET 整合或混合式部署連線，或前往 [資源的虛擬網路連線。 您還是可以使用兩個這些功能來存取的資源仍在未連線到您的虛擬網路的網路。

例如，您可以使用 VNET 整合與虛擬網路位於您的訂閱，但無法連線至您 ASE 處於虛擬網路的整合。 您仍然也可以存取資源的其他網路，就像您通常可以使用混合式連線。  

如果您已設定 ExpressRoute VPN 虛擬網路，您應該注意的一些 ASE 具有路由需求。 有一些使用者定義的路徑 (UDR) 設定與 ASE 不相容的。 如需執行 ASE ExpressRoute 的虛擬網路中的詳細資訊，請參閱[執行 ExpressRoute 的虛擬網路中的應用程式服務環境][ExpressRoute]。

#### <a name="securing-inbound-traffic"></a>保護輸入的流量

有兩個主要的方法來控制您 ASE 傳入的流量。  您可以使用網路安全性 Groups(NSGs) 來控制哪些 IP 位址可以存取您 ASE，如下所述[如何控制輸入的流量的應用程式服務環境中](app-service-app-service-environment-control-inbound-traffic.md)和您也可以使用內部的載入 Balancer(ILB) 設定您的 ASE。  這些功能也可在一起如果您想要使用您 ILB ASE 到 NSGs 限制存取。

當您建立 ASE 時，就會建立 VIP 您 VNet 中。  有兩種 VIP，內部及外部。  當您建立外部 VIP ASE 您的應用程式，在您 ASE 會透過網際網路路由傳送 IP 位址。 當您選取 [內部您 ASE 會以 ILB 設定，並不會直接存取網際網路。  ILB ASE 仍需要外部 VIP，但只會用 Azure 管理與維護存取。  

ILB ASE 建立期間您提供使用 ILB ASE 子網域，而且必須以管理您自己的您指定的子網域的 DNS。  因為您設定網域名稱，您也需要管理 HTTPS 存取所使用的憑證。  ASE 建立後會提示您提供的憑證。  若要瞭解更多關於建立及使用 ILB ASE 閱讀[使用應用程式服務環境內部負載平衡器][ILBASE]。 


## <a name="portal"></a>入口網站

您可以管理並監控您的應用程式服務環境 Azure 入口網站中使用的使用者介面。 如果您有 ASE，即表示您可能會看到您的資訊看板上的應用程式服務符號。 這個符號用來代表應用程式服務環境中 Azure 入口網站︰

![應用程式服務環境符號][1]

若要開啟列出所有應用程式服務環境使用者介面，您可以使用的圖示，或選取 > 形箭號 (「 > 」 符號) 以選取應用程式服務環境資訊看板底部。 選取其中一個列出 ASEs，您可以開啟用來監視及管理其使用者介面。

![監控和管理您的應用程式服務環境的使用者介面][2]

此第一把會顯示您 ASE，某些屬性，以及每個資源集區公制圖表。 一些**基本**區塊中所顯示的屬性，也會將會開啟與其相關聯刀的超連結。 例如，您可以選取**虛擬網路**名稱以開啟您 ASE 正在執行中的虛擬網路相關聯的 UI。 **應用程式服務方案**與**應用程式**每個開啟刀的清單中您 ASE 這些項目。  

### <a name="monitoring"></a>監控

圖表可以讓您查看各種不同的每個資源資料庫中的效能指標。 針對前端集區中，您可以監視平均的 CPU 和記憶體。 背景工作集區，您可以監視使用數量] 與 [可用的數量。

方案進行的多個應用程式服務使用的背景工作集區的員工。 工作負載分散相同的方式與前端伺服器，因此 CPU 和記憶體使用量不提供太多有用的資訊。 請務必更追蹤多少您使用的同事和都提供，尤其是如果您正在管理其他人使用這個系統。  

您也可以使用的度量，可追蹤的所有圖表] 中設定通知。 設定通知以下運作方式相同為其他地方在應用程式服務。 您可以設定提醒，從**通知**UI 部分或切入至任何指標 UI，然後選取 [**新增提醒**。

![指標 UI][3]

只要討論指標是應用程式服務環境指標。 也有可供使用的應用程式服務方案層級的指標。 這是監控 CPU 和記憶體讓合理的位置。

ASE，在所有應用程式服務計劃是專用的應用程式服務方案。 這表示的唯一的應用程式執行配置給主機上的應用程式服務方案位於應用程式的應用程式服務方案。 若要查看您的應用程式服務方案的詳細資料，顯示您的應用程式服務方案從任何 ASE ui 上清單或**瀏覽應用程式服務方案**（會列出所有）。   

### <a name="settings"></a>設定

ASE 刀，內有**設定**] 區段包含幾項重要功能︰

**設定** > **屬性**︰**設定**刀時自動開啟您顯示您 ASE 刀。 按一下頂端的**屬性**。 有幾個所多餘您在 [**基本資訊**，請參閱以下項目，而什麼是非常實用**虛擬 IP 位址**，以及**輸出 IP 位址**。

![設定刀和屬性][4]

**設定** > **IP 位址**︰ 當您在您 ASE 建立 IP 安全通訊端層 (SSL) 應用程式時，您需要 SSL IP 位址。 若要取得一個，您 ASE 必須 IP SSL 的地址擁有可以配置。 建立 ASE 時，它有一個 SSL IP 位址達到這個目的，但您可以新增更多。 有收費的其他 IP SSL 地址，如下圖所示，在[應用程式服務價格][ AppServicePricing] （在 SSL 連線的章節）。 其他的價格是 IP SSL 價格。

**設定** > **前端集區** / **工作者集區**︰ 每個這些資源集區刀可讓您查看資訊僅在該資源資料庫，除了提供完全不按比例縮放的資源資料庫的控制項。  

每個資源資料庫的基本刀提供的資源資料庫圖表中的指標。 就像 ASE 刀中，從圖表也可以移到圖表，並設定為所要的提醒。 從 ASE 刀特定資源集區設定通知所執行的動作以執行該資源資料庫中。 從工作集區**設定**刀，您有權存取的所有應用程式或應用程式服務方案執行這個工作集區中。

![背景工作集區設定使用者介面][5]

### <a name="portal-scale-capabilities"></a>入口網站的縮放功能  

有三個小數位數作業︰

- 變更數字的 IP 位址 ASE 所使用的 IP SSL 使用方式。
- 變更計算資源所使用的資源資料庫的大小。
- 變更使用的資源資料庫，手動或透過自動縮放計算資源的數目。

在入口網站中，有三種方法來控制您在您的資源資料庫中有多少伺服器︰

- 從主 ASE 刀頂端縮放作業。 您可以對多個小數位數的設定變更的前端與工作者集區。 所有套用為單一的作業。
- 從個別的資源資料庫**比例**刀，也就是在 [**設定**手動的縮放作業。
- 自動縮放，從個別的資源資料庫的**縮放比例**刀設定。

若要使用 ASE 刀的 [小數位數] 作業，請拖曳滑桿至您想要和儲存的數量。 這個 UI 也支援變更大小。  

![縮放比例 UI][6]

若要在特定的資源資料庫中使用手冊或自動調整大小的功能，請移至 [**設定** > **前端集區** / 適當的**工作者集區**。 然後開啟您想要變更的資料庫。 移至 [**設定** > **延展**或**設定** > **不按比例縮放**。 **縮放比例出**刀可讓您控制執行個體數量。 **縮放比例設定**可讓您控制資源大小。  

![不按比例縮放設定使用者介面][7]

## <a name="fault-tolerance-considerations"></a>容錯能力考量

您可以設定使用最 55 總計算資源的應用程式服務環境。 這些 55 的計算資源，只 50 可以用於主機負載。 原因是兩個。 有 2 前端計算資源的最小值。  會保留最多可支援工作者集區配置 53。 若要提供容錯，您需要有其他計算資源配置根據下列規則︰

- 每個工作者集區必須至少 1 其他計算資源的不是可以指派的工作量。
- 當背景工作集區中的計算資源的數量上方為特定的值，另一個計算資源是必要的容錯能力。 這不是在前端集區的大小寫。

內任何單一工作者集區的容錯能力需求為的資源分派給背景工作集區 X 指定值的︰

- 如果 X 是 2 到 20 之間，您可以使用工作負載的可用計算資源的量是 X-1。
- 如果 X 為 21 和 40 之間，您可以使用工作負載的可用計算資源的量是 X-2。
- 如果 X 為 41 之間 53，您可以使用工作負載的可用計算資源的量是 X-3。

最小值所需有 2 的前端伺服器與 2 的同事。  使用上述陳述式，以下是釐清的一些範例︰  

- 如果您有 30 工作者單一集區時，這些 28 可以使用主機工作量。
- 如果您有 2 工作者單一集區時，然後 1 可用於主機負載。
- 如果您有 20 工作者單一集區時，然後 19 可用於主機負載。  
- 如果您有 21 工作者單一集區時，然後仍只 19 可用於主機負載。  

容錯能力長寬很重要，但您需要請記住，當您調整超過特定閾值。 如果您想要新增更多的容量從 20 執行個體，然後移至 22 或更高因為 21 不新增任何其他的容量。 也是一樣移上方 40，將 [容量的下一個數字為 42 的位置。  

## <a name="deleting-an-app-service-environment"></a>刪除的應用程式服務環境 ##

如果您想要刪除的應用程式服務環境，然後直接使用 [**刪除**] 動作頂端的應用程式服務環境刀。 當您執行此動作時，系統會提示您輸入您的應用程式服務環境，以確認您確實想執行此動作的名稱。 當您刪除應用程式服務環境時，您刪除所有內的內容，也請注意。  

![刪除的應用程式服務環境使用者介面][9]  

## <a name="getting-started"></a>快速入門

若要開始使用應用程式服務環境，請參閱[如何建立應用程式服務環境](app-service-web-how-to-create-an-app-service-environment.md)。

如需有關 Azure 應用程式服務平台的詳細資訊，請參閱[Azure 應用程式服務](../app-service/app-service-value-prop-what-is.md)。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
