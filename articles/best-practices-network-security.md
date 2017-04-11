<properties
   pageTitle="Microsoft 雲端服務和網路安全性 |Microsoft Azure"
   description="瞭解索引鍵的功能，可協助建立安全的網路環境 Azure 中的部分"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="jonor;sivae"/>

# <a name="microsoft-cloud-services-and-network-security"></a>Microsoft 雲端服務及網路的安全性

Microsoft 雲端服務進行 hyperscale 服務與基礎結構、 企業級功能和許多選項可選混合式連線。 客戶可以選擇以存取這些服務，透過網際網路或 Azure ExpressRoute，提供私人網路連線。 Microsoft Azure 平台可讓流暢地將其基礎結構擴充至雲端，並建立多層架構提供的客戶。 此外，協力廠商可以啟用增強的功能提供安全性服務及虛擬設備。 這本白皮書提供安全性和客戶使用 Microsoft 雲端服務，透過 ExpressRoute 存取時，應考慮的架構問題的概觀。 同時說明 Azure 虛擬網路中建立更安全的服務。

## <a name="fast-start"></a>快速入門
下列邏輯圖表可以直接使用您用於 Azure 平台的許多安全性技術的特定範例。 快速參考，找出最符合您的個案的範例。 如更完整的說明，繼續閱讀本文。
![安全性選項] 流程圖][0]

[範例 1︰ 建立周邊網路 （也稱為 DMZ、 戰區及篩選的子網路） 來協助保護應用程式與網路安全性群組 (NSGs)。](#example-1-build-a-simple-dmz-with-nsgs)</br>
[範例 2︰ 建立周邊網路，以協助保護防火牆與 NSGs 應用程式。](#example-2-build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs)</br>
[範例 3︰ 建立以協助保護網路使用防火牆、 使用者定義的路徑 (UDR) 及 NSG 周邊網路。](#example-3-build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg)</br>
[範例 4︰ 新增至網站、 虛擬應用裝置虛擬私人網路 (VPN) 的混合式連線。](#example-4-adding-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[範例 5︰ 新增至網站、 Azure 閘道器 VPN 的混合式連線。](#example-5-adding-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn)</br>
[範例 6︰ 新增 ExpressRoute 的混合式連線。](#example-6-adding-a-hybrid-connection-with-expressroute)</br>
新增虛擬網路、 高可用性和服務鏈結之間的連線的範例會新增至這份文件，在未來幾個月。

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Microsoft 規範與基礎結構的保護
Microsoft 已經支援法規 enterprise 客戶所需的領導位置。 以下是一些 Azure 規範認證︰ ![Azure 規範徽章][1]

如需詳細資訊，請參閱[Microsoft 信任中心](https://azure.microsoft.com/support/trust-center/compliance/)的規範的資訊。

Microsoft 具有全面涵蓋所有內容的方法來保護執行 hyperscale 全域服務所需的雲端基礎架構。 Microsoft 雲端基礎架構包含硬體、 軟體、 網路，及管理及作業的人員，除了實體資料中心。

![Azure 安全性功能][2]

此方法均提供更安全基礎的部署 Microsoft 雲端服務的客戶。 下一步是用來設計和建立保護這些服務的安全性架構的客戶。

## <a name="traditional-security-architectures-and-perimeter-networks"></a>傳統的安全性架構與周邊網路
雖然 Microsoft 大量 invests 保護雲端基礎架構，客戶必須也會保護，其雲端服務與資源群組。 安全性的多層次的方法提供最佳的措施。 周邊網路安全性區域會防止不受信任的網路中的內部網路資源。 周邊網路指的是邊或網際網路與受保護的企業 IT 基礎結構之間坐網路的組件。

典型的企業網路中的核心基礎結構磐石在多個圖層的安全性裝置使用的周邊。 各個圖層的邊界是由裝置和原則強制執行點所組成。 裝置可能包括下列︰ 防火牆、 分散式拒絕的服務防止、 侵入偵測或保護系統 (識別碼/IPS) 及 VPN 裝置。 強制原則可能需要防火牆原則、 存取控制清單 (Acl)，或特定路由的表單。 在網路中，直接接受傳入的流量從網際網路防護的第一行是允許進一步合法要求到網路時封鎖攻擊及有害流量這些機制組合。 此流量路由直接對周邊網路中的資源。 資源可能再 「 討論 」 資源更深入的網路，transiting 驗證的下邊界中第一個。 最外層的圖層稱為周邊網路，因為此網路的一部份公開至網際網路，通常與某種雙面的保護。 下圖顯示在公司網路中，使用兩個安全性界限的單一子網路周邊網路的範例。

![公司網路的周邊網路][3]

有許多經常用來執行周邊網路，從簡單的負載平衡器前面的網頁伺服器陣列中，在每個邊界以封鎖流量及保護更深入的圖層的公司網路的分色機制的多個子網路周邊網路。 建置周邊網路的方式取決於組織和其整體的風險了的特定需求。

為客戶至公用雲朵移動他們的工作量，請務必支援符合規範與安全性需求 Azure 中的周邊網路架構類似的功能。 這份文件上客戶可以如何建立安全網路環境 Azure 中的提供的方針。 著重於周邊網路，但也包含各種網路安全性的完整討論。 下列問題會影響此討論︰

- 如何建置 Azure 中的周邊網路？
- 有哪些可用來建立的周邊網路的 Azure 功能？
- 後端負載可以如何保護？
- 網際網路通訊控制至 Azure 中的工作負載的？
- 內部部署網路，要如何保護從 Azure 中部署？
- 何時原生 Azure 安全性功能使用協力廠商裝置或服務與？

下圖顯示安全性 Azure 提供給客戶的各種層的級。 這些圖層是在本身的 Azure 平台的原生和客戶定義的功能︰

![Azure 安全性架構][4]

從網際網路]，可協助防範大型攻擊 Azure Azure DDoS 連入。 下一層是客戶定義公開端點，這用來決定哪些流量可以虛擬網路時通過雲端服務。 原生 Azure 虛擬網路隔離可確保完成隔離的其他所有的網路，及流量只接續透過使用者的設定路徑和方法。 這些路徑和方法的下一個層級]，其中 NSGs UDR，與網路虛擬應用裝置可用於建立安全界限保護受保護的網路中的應用程式部署。

下一節提供 Azure 虛擬網路的概觀。 這些虛擬網路所建立的客戶，且其部署的負載已經連線到。 虛擬網路是建立周邊網路保護客戶部署 Azure 中的所需的所有網路安全性功能的基礎。

## <a name="overview-of-azure-virtual-networks"></a>Azure 虛擬網路的概觀
Azure 虛擬網路能存取網際網路流量之前，有兩個層級的安全性固有 Azure 平台︰

1.  **DDoS 保護**︰ DDoS 保護功能是 Azure 實體網路大型以網際網路為基礎的遭受保護 Azure 平台本身的圖層。 這些攻擊使用，嘗試多個 「 傀儡 」 的節點，會使不勝負荷網際網路服務。 Azure 有強大的 DDoS 保護網狀結構上所有輸入的網際網路連線。 此 DDoS 保護層沒有任何使用者設定屬性，而且不會存取客戶。 為大型攻擊的平台保護 Azure，但會直接保護個別客戶應用程式。 當地語系化攻擊客戶，您可以設定彈性的其他圖層。 例如，如果客戶的已攻擊的大型 DDoS 攻擊公用的端點上，Azure 封鎖該服務的連線。 客戶的可能無法移到另一個虛擬網路或服務與還原服務攻擊不相關的結束點。 請注意雖然客戶的可能會影響該端點上，會受到影響其他服務外的結束點。 此外，其他客戶和服務會看到該攻擊沒有影響。
2.  **服務端點**︰ 結束點允許雲端服務] 或 [資源群組公用網際網路 IP 位址和連接埠。 端點會使用網路位址轉譯 (NAT) 若要將流量路由傳送至內部的地址和 Azure 虛擬網路的連接埠。 這是要傳入虛擬網路外部流量的主要路徑。 服務端點是傳入可設定的使用者，以決定哪些流量，和方式及位置翻譯至虛擬網路上。

一旦流量達到虛擬網路，有許多功能，進入 [播放]。 Azure 虛擬網路是附加他們的工作量，並套用基本網路層級安全性的位置與客戶的基礎。 下列功能與特性的客戶 Azure 中是私人網路 （虛擬網路覆疊）︰
 
- **流量隔離**︰ 虛擬網路是 Azure 平台上的流量隔離邊界。 即使兩個虛擬網路建立相同的客戶，直接對 Vm 在不同的虛擬網路中，無法進行通訊虛擬網路中的虛擬機器 (Vm)。 這是一個重要的內容，以確保客戶 Vm 及通訊保持私密虛擬網路中。
- **多層拓撲**︰ 虛擬網路允許客戶，以定義多層拓撲配置子網路，並指定不同的項目或 「 層 」，其工作負載的不同地址空格。 這些邏輯群組拓撲讓客戶定義的工作量類型，根據不同的存取原則，並控制層之間的流量。
- **跨內部部署連線**︰ 客戶可以建立 Azure 虛擬網路和多個內部部署網站或其他虛擬網路之間的跨內部部署連線。 若要這麼做，Azure VPN 閘道器或協力廠商的網路虛擬就可以使用客戶。 Azure 支援使用標準 IPsec/IKE 通訊協定和 ExpressRoute 私人連線至網站 (S2S) Vpn。
- **NSG**允許在所要的層級的建立規則 (Acl) 客戶︰ 網路介面、 個別 Vm 或虛擬子網路。 客戶可以藉由騰出更多或拒絕在虛擬網路，從系統跨內部部署連線，透過客戶的網路上的工作負載之間的通訊控制存取權，或直接網際網路通訊。
- **UDR**及**IP 轉寄功能**可讓客戶定義虛擬網路中的不同層之間的通訊路徑。 客戶，可以部署防火牆、 識別碼/IPS 和其他虛擬的裝置，並透過安全性邊界原則強制、 稽核、 和檢查這些安全性就路由傳送網路流量。
- Azure Marketplace 的**網路虛擬應用裝置**︰ 例如防火牆、 負載平衡器，以及識別碼/IPS 安全性就可使用 Azure Marketplace 和 VM 圖像庫中。 客戶可以完成多層安全的網路環境中部署到他們的虛擬網路，及特別是在其 （包括周邊網路子網路） 的安全性界限下列設備。

使用這些功能與功能的周邊網路架構可能會如何建構 Azure 中的其中一個範例如下︰

![Azure 虛擬網路中的周邊網路][5]

## <a name="perimeter-network-characteristics-and-requirements"></a>周邊網路特性與需求
周邊網路被專為網路，直接連接的網際網路通訊的前端。 內送的封包應該通過安全性應用裝置防火牆、 識別碼，以及 IPS，例如前與後端伺服器。 離開網路前，工作負載的網際網路繫結封包也可以傳送的周邊網路安全性裝置強制原則、 檢查，及稽核作業，透過。 此外，周邊網路可以主控客戶虛擬網路與內部部署網路之間的跨內部部署 VPN 閘道器。

### <a name="perimeter-network-characteristics"></a>周邊網路特性
參照上圖，部分特性的好的周邊網路如下所示︰

- 具網際網路︰
    - 本身的周邊網路子網路是具網際網路，直接與網際網路通訊。
    - 公用 IPs、 Vip，及/或服務端點，請將網際網路流量傳遞至前端網路和裝置。
    - 從網際網路輸入的流量經過前端網路上的其他資源之前的安全性裝置。
    - 如果已啟用輸出的安全性，流量通過安全性裝置的最後一個步驟，傳遞到網際網路之前。
- 受保護的網路︰
    - 沒有直接從網際網路路徑核心基礎結構。
    - 核心基礎結構的頻道必須通過透過安全性裝置，例如 NSGs、 防火牆或 VPN 裝置。
    - 其他裝置必須橋網際網路和核心基礎結構。
    - 受保護的網路對周邊網路 （例如，兩個防火牆圖示上圖所示） 的界限和具網際網路上的安全性裝置實際上可能是單一虛擬應用裝置差異的規則或介面的每個邊界。 （也就一個裝置，以邏輯方式分隔，處理周邊網路的兩個界限的載入）。
- 其他一般做法和限制︰
    - 負載必須儲存商務重要資訊。
    - 存取和周邊網路設定和部署更新限於授權的系統管理員。

### <a name="perimeter-network-requirements"></a>周邊網路需求
若要啟用這些特性，請遵循這些指導方針虛擬網路需求，以執行成功的周邊網路上︰

- **子網路架構︰**經過的整個子網路致力於為周邊網路，分開的相同的虛擬網路中其他子網路，請指定虛擬網路。 這樣的周邊網路和其他透過防火牆或識別碼/IP 子網路界限與使用者定義的路徑上的虛擬應用裝置的內部或私人子網路層流程之間的流量。
- **NSG:**本身的周邊網路子網路應該開啟允許與網際網路通訊，但這不表示客戶應該略過 NSGs。 依照一般安全性作法最小化網際網路上公開此網路介面。 鎖定允許存取部署或特定應用程式的通訊協定和開啟的連接埠遠端位址範圍。 可能的情況下，不過，這是在不一定可能。 例如，如果客戶 Azure 中外部網站，周邊網路應該允許來自網站要求任何的公用 IP 位址，但只能開啟 web 應用程式連接埠︰ TCP:80 和 TCP:443。
- **路由表格︰**本身的周邊網路子網路應會直接，通訊至網際網路，但不透過防火牆或安全性的應用裝置不應該允許直接通訊與背面的結尾或內部網路。
- **安全性應用裝置設定︰**項目才路由與檢查之間的周邊網路及其餘的受保護的網路，例如防火牆、 識別碼，以及 IPS 安全性就封包裝置可能多重。 可能會有個別 Nic 周邊網路和後端子網路。 在周邊網路 Nic 傳達直接從網際網路]，與相對應的 NSGs 周邊網路路由表格。 連線到後端子網路 Nic 有更多受限 NSGs 和對應的後端子網路路由表格。
- **安全性應用功能︰**通常部署的周邊網路安全性就會執行下列功能︰
    - 防火牆︰ 強制防火牆規則] 或 [內送的要求存取控制 」 原則。
    - 威脅偵測並防止︰ 偵測並降低惡意攻擊從網際網路。
    - 稽核和記錄︰ 維護稽核和分析詳細的記錄。
    - 反向 proxy︰ 重新導向至對應的後端伺服器的傳入的邀請。 這包含對應和翻譯的前端的裝置上的目的地位址通常是防火牆、 的後端伺服器位址。
    - 轉寄 proxy︰ 提供 NAT，並執行稽核從虛擬網路中啟動網際網路通訊。
    - 路由器︰ 轉接虛擬網路內部的內送與跨子網路流量。
    - VPN 裝置︰ 做為交叉內部部署 VPN 閘道器的客戶內部網路和 Azure 虛擬網路之間的跨內部部署 VPN 連線。
    - VPN 伺服器︰ 接受 VPN 用戶端連線至 Azure 虛擬網路。

>[AZURE.TIP] 分開下列兩個群組︰ 個別使用者的權限存取周邊網路安全性齒輪和應用程式開發、 部署或作業系統管理員的身分授權的人員。 保留這些群組個別的責任分隔並略過的應用程式的安全性和網路的安全性控制項時，防止一個人。

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>建立網路界限時，系統會要求的問題
在此區段中，特別是所述，除非字詞 「 網路 」 指的是私人 Azure 虛擬網路訂閱管理員所建立。 Azure 中的基礎實體網路並未參照字詞。

此外，Azure 虛擬網路通常用來擴充傳統的內部部署網路。 就可以加入到網站或 ExpressRoute 混合式網路解決方案的周邊網路架構。 這是建築物網路安全性的重要考量。

下列三個問題的回答您正在建立的周邊網路與多個安全性界限網路時要徑。

#### <a name="1-how-many-boundaries-are-needed"></a>需要 1） 多少界限？
第一個的決策點會決定多少安全性界限所需在特定的案例︰

- 單一的邊界︰ 前端之間的周邊網路，虛擬網路和網際網路上。
- 兩個界限︰ 網際網路方的周邊網路，以及另一個周邊網路的子網路和 Azure 虛擬網路中的後端子網路之間的其中一個。
- 三個界限︰ 網際網路旁的周邊網路上的周邊網路與後端子網路之間的項目，然後的後端子網路與內部部署網路之間的項目。
- N 界限︰ 變數數字。 根據的安全性需求，有的安全性界限可以指定網路中套用的實際的數字。

數字與類型界限需要而異公司的風險了和實作特定案例為基礎。 這通常是聯合決策所做的組織，通常包括風險與規範小組、 網路與平台小組和應用程式開發小組中的多個群組。 安全性、 資料，以及正在使用的技術的人士應有說中以確保每個實作適當的安全性戰術此決策。

>[AZURE.TIP] 使用 [最小邊界符合特定情況的安全性需求的數目。 使用多個界限更加困難作業與疑難排解可，以及管理費用略述與管理多個邊界原則一段時間。 不過，沒有足夠的界限增加風險。 尋找餘額很重要。

![使用三個安全性界限的混合式網路][6]

先前的圖表中顯示三個安全性邊界網路的高層級檢視。 是的周邊網路和網際網路、 Azure 前端與後端私人子網路，及 Azure 的後端子網路與公司內部網路之間的邊界。

#### <a name="2-where-are-the-boundaries-located"></a>2） 邊界位於何處？
後會決定界限數，實作它們的位置會是下一個的決策點。 通常，有三個選項︰
- 使用網際網路中繼服務 （例如，雲端 Web 應用程式防火牆來說，這份文件不討論）
- 使用原生功能及/或網路虛擬應用裝置 Azure 中
- 內部網路上使用實體裝置

完全 Azure 在網路上，原生 Azure 功能 （例如，Azure 負載平衡器） 或網路虛擬裝置豐富的合作夥伴生態從 Azure 的 （例如，檢查點防火牆） 就的選項。

如果邊界需要 Azure 及內部部署網路之間，安全性裝置可以存放在任一側的連線 （或兩側）。 因此必須決定位置上放置安全性齒輪。

在上圖中，網際網路的周邊網路和上層到後端邊界完全包含在 Azure 內，而且必須是原生 Azure 功能或網路虛擬設備。 Azure 側邊或在內部部署側邊，或甚至雙面裝置的組合，可能是在 Azure （後端子網路） 與公司網路之間的邊界上的安全性裝置。 可以有明顯優缺點必須會被視為任何一個選項。

例如，使用現有的實體安全性齒輪內部部署網路端的優點，需要沒有新的齒輪。 只要重新設定。 然而，缺點是，所有流量必須都來自回 Azure 到內部部署網路的安全性齒輪能夠看到。 因此 Azure-Azure 流量可能會造成嚴重的延遲時間，並會影響應用程式的效能與使用者體驗，如果它強制回到內部網路上的安全性原則強制執行。

#### <a name="3-how-are-the-boundaries-implemented"></a>3） 如何實作邊界？
每個安全性邊界可能會有不同的功能需求 （例如，識別碼和網際網路一邊的周邊網路，但只 Acl 的周邊網路與後端子網路之間的防火牆規則）。 決定要使用的裝置而定的案例和安全性需求。 在 [動作] 區段範例 1、 2 和 3 討論無法使用某些選項。 檢閱 Azure 原生網路功能與合作夥伴生態從 Azure 中可用的裝置，就會顯示可用來解決幾乎任何案例的各種選項。

另一個金鑰實作決策點會說明如何將內部部署網路連線與 Azure。 您應該使用 Azure 虛擬閘道器或網路虛擬應用裝置？ 下節 （範例 4、 5 和 6） 中的更詳細地討論這些選項。

此外，您可能需要之間內 Azure 虛擬網路流量。 之後，便會新增這些案例。

您知道上一個問題的答案，[[快速入門](#fast-start)] 區段可協助您識別是最適合用於特定案例的範例。

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>範例︰ 建置安全性界限與 Azure 虛擬網路
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>範例 1︰ 建立周邊網路，以協助保護 NSGs 的應用程式
[回到快速開始](#fast-start) | [詳細資料建立此範例中的指示][Example1]

![輸入與 NSG 的周邊網路][7]

#### <a name="environment-description"></a>環境描述
在此範例中，有訂閱包含下列動作︰

- 兩個 cloud services: 「 FrontEnd001 」 和 「 BackEnd001 」
- 虛擬網路，「 CorpNetwork 」，具有兩個子網路: 「 FrontEnd 」 和 「 後端 」
- 套用至這兩個子網路安全性群組
- Windows 伺服器代表應用程式網頁伺服器 (「 IIS01 」)
- 兩個 Windows 伺服器代表應用程式後端伺服器 （「 AppVM01 」、 「 AppVM02 」）
- Windows 伺服器代表 DNS 伺服器 (「 DNS01 」)

為指令碼和 Azure 資源管理員範本，請參閱[詳細的建立指示][Example1]。

#### <a name="nsg-description"></a>NSG 描述
在此範例中，會 NSG 群組內建，然後再載入六個規則。

>[AZURE.TIP] 一般而言，您應該建立特定的 「 允許 」 規則，後面接著一般的 「 拒絕 」 規則。 指定的優先順序指出會先評估的規則。 一旦流量找到要套用至某特定規則時，會不評估任何進一步的規則。 （從子網路的觀點） 輸入或輸出方向，可以套用 NSG 規則。

宣告方式，是輸入流量建置下列規則︰

1.  允許內部 DNS 流量 （連接埠 53）。
2.  允許 RDP （連接埠 3389） 來自網際網路流量到任何的虛擬機器。
3.  允許 HTTP （連接埠 80） 來自網際網路流量網頁伺服器 (IIS01)。
4.  允許任何流量 （所有連接埠） IIS01 AppVM1。
5.  拒絕任何 （所有連接埠） 來自網際網路流量整個虛擬網路 （兩個子網路）。
6.  拒絕任何流量 （所有連接埠） 的前端子網路到後端的子網路。

使用這些規則繫結至每一個子網路，如果 HTTP 要求輸入從網際網路網頁伺服器，而這兩種規則 3 （允許） 和 5 （拒絕） 會套用。 但是由於規則 3 有較高的優先順序，只會套用，並規則 5 就不會發生。 因此網頁伺服器就會允許 HTTP 要求。 如果連線 DNS01 伺服器嘗試相同流量，規則 5 （拒絕） 就會套用，第一個和不想要允許流量傳送至伺服器。 規則 6 （拒絕） 封鎖從向後端子網路 （除了允許流量規則 1 到 4） 的前端子網路。 這可以保護後端網路，以防攻擊者入侵前端的 web 應用程式。 攻擊會有限制存取後端 」 受保護的 「 網路 （只對公開 AppVM01 伺服器上的資源）。

有預設的輸出規則，允許出的網際網路流量。 例如，我們是允許外寄流量，並不修改任何輸出規則。 若要鎖定兩個方向的流量，若要使用者定義路由不需要 （請參閱範例 3）。

#### <a name="conclusion"></a>結束時
這是隔離從輸入流量的後端子網路的簡單及乾淨的簡單方法。 如需詳細資訊，請參閱[建立詳細的指示][Example1]。 包含這些指示︰

- 如何建立使用 PowerShell 指令碼此周邊網路。
- 如何建立使用 Azure 資源管理員範本此周邊網路。
- 每個 NSG 命令的詳細的說明。
- 詳細的流量流向的情況下，顯示如何允許或拒絕各個圖層中的傳輸。


 ### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>範例 2︰ 建立周邊網路，以協助保護防火牆與 NSGs 應用程式
[回到快速開始](#fast-start) | [詳細資料建立此範例中的指示][Example2]

![輸入 NVA 與 NSG 的周邊網路][8]

#### <a name="environment-description"></a>環境描述
在此範例中，有訂閱包含下列動作︰

- 兩個 cloud services: 「 FrontEnd001 」 和 「 BackEnd001 」
- 虛擬網路，「 CorpNetwork 」，具有兩個子網路: 「 FrontEnd 」 和 「 後端 」
- 套用至這兩個子網路安全性群組
- 網路的虛擬應用，在本例中的防火牆，以連線至前端子網路
- Windows 伺服器代表應用程式網頁伺服器 (「 IIS01 」)
- 兩個 Windows 伺服器代表應用程式後端伺服器 （「 AppVM01 」、 「 AppVM02 」）
- Windows 伺服器代表 DNS 伺服器 (「 DNS01 」)

為指令碼和 Azure 資源管理員範本，請參閱[詳細的建立指示][Example2]。

#### <a name="nsg-description"></a>NSG 描述
在此範例中，會 NSG 群組內建，然後再載入六個規則。

>[AZURE.TIP] 一般而言，您應該建立特定的 「 允許 」 規則，後面接著一般的 「 拒絕 」 規則。 指定的優先順序指出會先評估的規則。 一旦流量找到要套用至某特定規則時，會不評估任何進一步的規則。 （從子網路的觀點） 輸入或輸出方向，可以套用 NSG 規則。

宣告方式，是輸入流量建置下列規則︰

1.  允許內部 DNS 流量 （連接埠 53）。
2.  允許 RDP （連接埠 3389） 來自網際網路流量到任何的虛擬機器。
3.  允許網路虛擬應用裝置 （防火牆） 任何網際網路流量 （所有連接埠）。
4.  允許任何流量 （所有連接埠） IIS01 AppVM1。
5.  拒絕任何 （所有連接埠） 來自網際網路流量整個虛擬網路 （兩個子網路）。
6.  拒絕任何流量 （所有連接埠） 的前端子網路到後端的子網路。

使用這些規則繫結至每個子網路，如果 HTTP 要求輸入從網際網路的防火牆，這兩種規則 3 （允許） 和 5 （拒絕） 會套用。 但是由於規則 3 有較高的優先順序，只會套用，並規則 5 就不會發生。 因此防火牆就會允許 HTTP 要求。 如果該相同的流量嘗試連絡 IIS01 伺服器時，即使是在前端子網路，規則 5 （拒絕） 會套用，並不想要允許流量傳送至伺服器。 規則 6 （拒絕） 封鎖從向後端子網路 （除了允許流量規則 1 到 4） 的前端子網路。 這可以保護後端網路，以防攻擊者入侵前端的 web 應用程式。 攻擊會有限制存取後端 」 受保護的 「 網路 （只對公開 AppVM01 伺服器上的資源）。

有預設的輸出規則，允許出的網際網路流量。 例如，我們是允許外寄流量，並不修改任何輸出規則。 若要鎖定兩個方向的流量，若要使用者定義路由不需要 （請參閱範例 3）。

#### <a name="firewall-rule-description"></a>防火牆規則描述
在防火牆後，應建立轉接規則。 需要規則，因為此範例中只路由網際網路流量中繫結至防火牆]，然後網頁伺服器，只有一個轉接網路位址轉譯 (NAT)。

轉寄規則接受點擊防火牆嘗試連絡 HTTP （的連接埠 80 或 443 HTTPS） 的任何輸入的來源地址。 它具有傳送不在防火牆的本機介面，並重新導向到網頁伺服器 10.0.1.5 的 IP 位址。

#### <a name="conclusion"></a>結束時
這是相當簡單的方式保護您的應用程式的防火牆和隔離從輸入流量的後端子網路。 如需詳細資訊，請參閱[建立詳細的指示][Example2]。 包含這些指示︰

- 如何建立使用 PowerShell 指令碼此周邊網路。
- 如何建立此範例使用 Azure 資源管理員範本。
- 每個 NSG 命令和防火牆規則的詳細的說明。
- 詳細的流量流向的情況下，顯示如何允許或拒絕各個圖層中的傳輸。

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-udr-and-nsg"></a>範例 3︰ 建立以協助保護網路使用防火牆、 UDR 及 NSG 周邊網路
[回到快速開始](#fast-start) | [詳細資料建立此範例中的指示][Example3]

![NVA、 NSG，與 UDR 的雙向周邊網路][9]

#### <a name="environment-description"></a>環境描述
在此範例中，有訂閱包含下列動作︰

- 三個雲端服務: 「 SecSvc001 」、 「 FrontEnd001 」，以及 「 BackEnd001 」
- 虛擬網路，「 CorpNetwork 」，具有三個子網路: 「 SecNet 」、 「 FrontEnd 」，以及 「 後端 」
- 網路的虛擬應用，在本例中的防火牆，連線到 SecNet 子網路
- Windows 伺服器代表應用程式網頁伺服器 (「 IIS01 」)
- 兩個 Windows 伺服器代表應用程式後端伺服器 （「 AppVM01 」、 「 AppVM02 」）
- Windows 伺服器代表 DNS 伺服器 (「 DNS01 」)

為指令碼和 Azure 資源管理員範本，請參閱[詳細的建立指示][Example3]。

#### <a name="udr-description"></a>UDR 描述
根據預設，下列系統路由定義為︰

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal 永遠是虛擬網路上的特定網路的已定義之地址 prefix(es) （也就是它變成從虛擬網路虛擬網路，根據每個特定的虛擬網路定義的方式而定）。 剩餘系統路由靜態，而且預設為資料表中指定。

在此範例中，傳遞的兩個資料表建立，分別為前端與後端的子網路。 每個資料表會載入靜態路由適用於指定子網路。 此範例中，針對每個資料表有將所有流量導向 (0.0.0.0 / 0) 的三個路由透過防火牆 (下一步躍點 = 虛擬應用裝置 IP 位址):

1. 使用沒有下一個躍點的本機子網路流量定義，以允許略過防火牆區域子網路流量。
2. 虛擬網路流量與下一個躍點定義為防火牆。這會覆寫預設的規則，允許本機虛擬網路流量路由傳送直接。
3. 所有剩餘流量 (0/0)，與下一個躍點定義為防火牆。

>[AZURE.TIP] UDR 沒有區域子網路項目，將會中斷區域子網路通訊。 
> - 在此範例中，指向 VNETLocal 10.0.1.0/24 很重要，否則封包離開網頁伺服器 (10.0.1.4)，指向 [其他本機伺服器 （例如） 10.0.1.25 將會失敗時它們會透過傳送至 NVA，其會傳送至子網路，請為並子網路會重新傳送給 NVA 等等。
> - 路由迴圈的機會是通常位置較高的每一個子網路，進行通訊，但這通常是傳統，內部部署，就會直接連接的多重 nic 設備。 

路由資料表建立之後，他們會繫結到他們的子網路。 [前端子網路路由] 資料表中一次建立並繫結至子網路，如下所示︰

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

>[AZURE.NOTE] UDR 現在可以套用至閘道器子網路的連線 ExpressRoute 電路。
>
> 若要啟用您的周邊網路 ExpressRoute 或網站-網路的範例所示範例 3 和 4。


#### <a name="ip-forwarding-description"></a>IP 轉寄功能描述
IP 轉接是 UDR 小幫手] 功能。 這是可以讓它接收流量不是收件者為應用裝置，然後將轉寄最終目的地資料的虛擬應用裝置上的設定。

例如，如果從 AppVM01 DNS01 伺服器提出要求時，UDR 想路由這防火牆。 使用 IP 轉寄啟用，DNS01 目的地 (10.0.2.4) 的流量接受應用裝置 (10.0.0.4)，而再轉寄給最終目的地 (10.0.2.4)。 不含 IP 轉寄防火牆上啟用，流量不會接受應用裝置，即使路由表有一個躍點為防火牆。 若要使用的虛擬應用裝置，請務必記得要啟用 UDR 及 IP 轉寄功能。

#### <a name="nsg-description"></a>NSG 描述
在此範例中，會 NSG 群組內建，並且然後載入以單一的規則。 此群組是繫結至前端與後端子網路 (不 SecNet)。 宣告方式建立下列規則︰

- 拒絕任何 （所有連接埠） 來自網際網路流量整個虛擬網路 （所有子網路）。

雖然在此範例使用 NSGs，主要目的是為防護，手動設定錯誤的第二層。 目標是要封鎖所有傳入的流量從網際網路到前端或後端子。 流量應該僅限通過防火牆 SecNet 子網路 (然後，視哪前端或後端子網路)。 此外，UDR 規則的位置，任何未納入的前端或後端子網路的流量會導向查看 （獎狀 UDR) 防火牆。 防火牆會看到為非對稱式的流程拖放想要的外寄流量。 因此，有三個層級的保護子網路的安全性︰
- 任何開啟的端點 FrontEnd001 和 BackEnd001 不雲端服務。
- 從網際網路拒絕流量 NSGs。
- 防火牆卸除非對稱式流量。

一個感興趣的點，在此範例中 NSG 有關是包含只有一個規則，也就是拒絕整個虛擬網路，包括安全性子網路的網際網路流量。 不過，由於 NSG 只繫結到前端與後端的子網路時，規則不會處理流量安全性子網路連入。 如此一來，將流向安全性子網路流量。

#### <a name="firewall-rules"></a>防火牆規則
在防火牆後，應建立轉接規則。 由於防火牆封鎖或轉接所有輸入、 輸出，並內虛擬網路流量，則需要多個防火牆規則。 此外，所有輸入的流量會點擊安全性服務公用 IP 位址 （在不同的連接埠）、 防火牆設定。 最佳作法是圖表邏輯之前設定子網路流量而防火牆規則，以避免稍後再重新。 下圖是防火牆規則，此範例中的邏輯檢視︰
 
![邏輯檢視的防火牆規則][10]

>[AZURE.NOTE] 根據所用的虛擬應用裝置網路，管理連接埠會視情況而定。 在此範例中，Barracuda NextGen 防火牆參考時，會使用連接埠 22 801，與 807。 查閱應用裝置廠商文件，若要尋找的確切的連接埠用來管理所用的裝置。

#### <a name="firewall-rules-description"></a>防火牆規則描述
在先前的邏輯圖表，不會顯示安全性子網路。 這是由於防火牆是唯一的資源在該子網路，而此圖表會顯示防火牆規則及如何他們邏輯允許或拒絕流量流，而非實際的路由路徑。 此外，選取 RDP 流量較高的外部連接埠遠距連接埠 (8014 – 8026)，然後選取要比較對齊更容易可讀性的本機 IP 位址的最後兩個八位元組 （例如，本機伺服器位址 10.0.1.4 為外部連接埠 8014 與相關聯）。 不過，可以使用任何高非衝突連接埠。

此範例中，我們需要七種規則類型︰

- 外部的規則 （輸入流量）︰
  1.    防火牆管理規則︰ 重新導向此應用程式的規則允許流量傳遞至網路虛擬應用裝置管理連接埠。
  2.    RDP 規則 （每個 Windows 伺服器）: （每個伺服器一個） 四個規則允許透過 RDP 個別伺服器的管理。 這也可能會建立一個規則，根據網路虛擬應用裝置所用的功能將。
  3.    應用程式的資料傳輸規則︰ 有兩個的前端網頁流量，第一個與後端流量 （例如，網頁伺服器資料層） 的第二個這些規則。 這些規則的設定網路架構 （您的伺服器放置的位置） 而定，以及流量流向 （流量，與哪些連接埠用的方向）。
      - 第一個規則可讓達到應用程式伺服器的實際的應用程式流量。 雖然其他規則，允許的安全性與管理，應用程式流量的規則以及允許外部使用者或服務存取應用程式。 此範例中，有單一網頁伺服器連接埠 80。 因此單一防火牆應用程式規則會重新導向輸入的流量外部 IP 的網頁伺服器內部 IP 位址。 內部伺服器，想要透過 NAT 翻譯流量重新導向工作階段。
      - 第二個規則是透過任何連接埠的後端規則，允許網頁伺服器對談 AppVM01 伺服器 （但不是 AppVM02）。
- 內部規則 （內虛擬網路流量）
  4.    輸出至網際網路規則︰ 此規則可讓任何網路傳遞到所選的網路流量。 此規則通常是預設規則已在防火牆後，但已停用狀態。 這個範例中，才可以啟用此規則。
  5.    DNS 規則︰ 此規則可讓只有 DNS （連接埠 53） 傳遞給 DNS 伺服器的流量。 此環境遭到封鎖從前端的後端的大部分流量。 此規則允許特定 DNS 的任何區域的子網路。
  6.    子網路規則的子網路︰ 此規則可允許任何伺服器端子網路連線到任何伺服器上的前端子網路 （但不是相反）。
- 保全 （用於不符合任何前一個的流量） 規則︰
  7.    拒絕所有的資料傳輸規則︰ 此一律採用 （而言優先順序），最後一個規則，因此如果交通流量無法符合任何前一個規則，將此規則卸除。 這是預設的規則，而且通常會啟動。 通常需要修改。

>[AZURE.TIP] 第二個應用程式的資料傳輸規則，以簡化此範例中，任何連接埠被允許]。 在真實案例中，最適合的連接埠與位址範圍應以減少此規則的攻擊。

所有的上一個規則建立之後，請務必要檢閱每一個規則，以確保會允許或拒絕視流量的優先順序。 例如，規則的順序優先順序。

#### <a name="conclusion"></a>結束時
這是較為複雜，但更完整的方式保護和隔離的網路，比上述範例。 範例 2 保護只應用程式，並範例 1 只將隔離子網路。 此設計監視流量兩個方向，可以讓及保護不只是連入應用程式伺服器，但會強制執行這個網路上的所有伺服器的網路安全性原則。 此外，根據使用的應用裝置，完整流量稽核和線上狀態，可以達成。 如需詳細資訊，請參閱[建立詳細的指示][Example3]。 包含這些指示︰

- 如何建立使用 PowerShell 指令碼此範例周邊網路。
- 如何建立此範例使用 Azure 資源管理員範本。
- 詳細描述每個 UDR，NSG] 命令，以及防火牆規則。
- 詳細的流量流向的情況下，顯示如何允許或拒絕各個圖層中的傳輸。

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-virtual-private-network-vpn"></a>範例 4︰ 新增至網站、 虛擬應用裝置虛擬私人網路 (VPN) 的混合式連線
[回到快速開始](#fast-start)|推出詳細建立相關指示

![與 NVA 連線的混合式網路的周邊網路][11]

#### <a name="environment-description"></a>環境描述
使用網路虛擬應用裝置 (NVA) 的混合式網路可以新增至任何範例 1、 2 或 3 中所述的周邊網路類型。

上圖所示，透過網際網路 （網站的網站） VPN 連線用來將內部部署網路連線到 NVA 透過 Azure 虛擬網路。

>[AZURE.NOTE] 如果您是使用 ExpressRoute 啟用 Azure 公用對等選項，則應該建立靜態路由。 此路由傳送至 NVA VPN IP 位址看您公司的網際網路]，並不透過 ExpressRoute WAN。 公用 ExpressRoute Azure 的對等選項所需的 NAT 可以中斷 VPN 工作階段。

VPN 放好之後，NVA 會變成所有的網路和子網路的集中。 防火牆轉寄規則決定哪些流量允許、 透過 NAT 轉譯、 會被重新導向，或會遭到捨棄 （即使的內部部署網路和 Azure 之間的流量）。

流量應該要謹慎，他們可以進行最佳化或降低此設計模式中，根據特定使用大小寫。

使用內建於範例 3 的環境，並在當中新增至網站 VPN 混合式網路連線，會產生下列設計︰

![使用網站-VPN NVA 周邊網路連線][12]

內部部署路由器或任何其他網路的裝置與您 NVA vpn，就會在 VPN 用戶端。 此實體裝置就會啟動並維持與您 NVA VPN 連線。

邏輯至 NVA，網路看起來像四個不同的 「 安全性區域 」 的規則正在流量這些區域之間的主要 director NVA 上︰

![邏輯網狀 NVA 觀點][13]

#### <a name="conclusion"></a>結束時
Azure 虛擬網路網站-VPN 混合式網路連線的可延伸至 Azure 內部部署網路，以安全的方式。 在使用 VPN 連線時，將流量已加密並路由透過網際網路。 在此範例中 NVA 提供強制執行和管理的安全性原則的中央位置。 如需詳細資訊，請參閱建立詳細的指示 （推出）。 包含這些指示︰

- 如何建立使用 PowerShell 指令碼此範例周邊網路。
- 如何建立此範例使用 Azure 資源管理員範本。
- 詳細的流量流向的情況下，顯示如何流量流向此設計。

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-gateway-vpn"></a>範例 5︰ 新增至網站、 Azure 閘道器 VPN 的混合式連線
[回到快速開始](#fast-start)|推出詳細建立相關指示

![閘道器連線的混合式網路的周邊網路][14]

#### <a name="environment-description"></a>環境描述
使用 Azure VPN 閘道器的混合式網路可以新增周邊網路鍵入 1 或 2 的範例中所述。

前圖所示，透過網際網路 （網站的網站） VPN 連線用來將內部部署網路連線透過 Azure VPN 閘道 Azure 虛擬網路。

>[AZURE.NOTE] 如果您是使用 ExpressRoute 啟用 Azure 公用對等選項，則應該建立靜態路由。 此路由傳送至 NVA VPN IP 位址看您公司的網際網路]，並不透過 ExpressRoute WAN。 NAT 必要公用 ExpressRoute Azure 的對等選項，可以中斷 VPN 工作階段。

下圖顯示兩個網路邊緣，在此選項。 在第一個邊緣，NVA 和 NSGs 控制內 Azure 網路和網際網路 Azure 之間的流量。 第二個邊緣是 Azure VPN 閘道，也就是內部部署和 Azure 之間，完全個別的隔離網路邊緣。

流量應該要謹慎，他們可以進行最佳化或降低此設計模式中，根據特定使用大小寫。

使用範例 1，內建的環境，然後再新增至網站 VPN 混合式網路連線，會產生下列設計︰

![使用連線至 ExpressRoute 連接的閘道器的周邊網路][15]

#### <a name="conclusion"></a>結束時
Azure 虛擬網路網站-VPN 混合式網路連線的可延伸至 Azure 內部部署網路，以安全的方式。 使用原生 Azure VPN 閘道器，將流量 IPSec 加密，而透過網際網路傳送。 此外，使用 Azure VPN 閘道器可提供較低的成本選項 （沒有其他授權如同協力廠商 NVAs 成本）。 這是最節省在範例 1，不 NVA 的使用位置。 如需詳細資訊，請參閱建立詳細的指示 （推出）。 包含這些指示︰

- 如何建立使用 PowerShell 指令碼此範例周邊網路。
- 如何建立此範例使用 Azure 資源管理員範本。
- 詳細的流量流向的情況下，顯示如何流量流向此設計。

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>範例 6︰ 新增 ExpressRoute 的混合式連線
[回到快速開始](#fast-start)|推出詳細建立相關指示

![閘道器連線的混合式網路的周邊網路][16]

#### <a name="environment-description"></a>環境描述
使用 ExpressRoute 私人對等連線的混合式網路可以新增周邊網路鍵入 1 或 2 的範例中所述。

前圖所示，ExpressRoute 私人對等提供您的內部網路和 Azure 虛擬網路之間的直接連線。 流量代表只服務提供者網路與 Microsoft Azure 網路，永遠不會碰到網際網路。

>[AZURE.NOTE] 使用 ExpressRoute，因為複雜的動態路由用於 Azure 虛擬閘道器 UDR 時，有特定的限制。 以下是，如下所示︰
>
>- UDR 不應該套用至閘道器子網路的連線 ExpressRoute 連結 Azure 虛擬閘道器。
>- 其他 UDR 個躍點裝置繫結子網路時，無法 ExpressRoute 連結 Azure 虛擬閘道器。
>
>
<br />

>[AZURE.TIP] 使用 ExpressRoute] 可保留公司網路流量人員 Internet 上為較高的安全性與大幅提升效能。 您也可以用服務等級協定從 ExpressRoute 提供者。 Azure 閘道器可以傳送最多 2 Gb/s 與 ExpressRoute，而網站-vpn Azure 閘道器的最大處理量 200 Mb/s。

下圖所示，使用此選項環境現在有兩個網路邊緣。 閘道器時，完全個別的隔離網路邊緣，內部部署和 Azure 之間，NVA 和 NSG 控制內 Azure 網路和 Azure 與網際網路之間的流量。

流量應該要謹慎，他們可以進行最佳化或降低此設計模式中，根據特定使用大小寫。

使用範例 1，內建的環境，然後再新增 [ExpressRoute 混合式部署的網路連線，會產生下列設計︰

![使用連線至 ExpressRoute 連接的閘道器的周邊網路][17]

#### <a name="conclusion"></a>結束時
ExpressRoute 私人 Peering 網路連線的加法可以將內部部署網路延伸至 Azure，在 [安全的較低的延遲，較高的執行方式。 此外，使用原生 Azure 閘道器，如下列範例中，提供較低的成本選項 （沒有其他授權如同協力廠商 NVAs）。 如需詳細資訊，請參閱建立詳細的指示 （推出）。 包含這些指示︰

- 如何建立使用 PowerShell 指令碼此範例周邊網路。
- 如何建立此範例使用 Azure 資源管理員範本。
- 詳細的流量流向的情況下，顯示如何流量流向此設計。

## <a name="references"></a>參照
### <a name="helpful-websites-and-documentation"></a>實用的網站和文件
- Access Azure 與 Azure 資源管理員︰
- 存取使用 PowerShell 的 Azure: [https://azure.microsoft.com/documentation/articles/powershell-install-configure/](./powershell-install-configure.md)
- 虛擬網路的文件︰ [https://azure.microsoft.com/documentation/services/virtual-network/](https://azure.microsoft.com/documentation/services/virtual-network/)
- 網路安全性] 群組中的文件︰ [https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/](./virtual-network/virtual-networks-nsg.md)
- 使用者定義傳閱文件︰ [https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/](./virtual-network/virtual-networks-udr-overview.md)
- Azure 虛擬閘道器︰ [https://azure.microsoft.com/documentation/services/vpn-gateway/](https://azure.microsoft.com/documentation/services/vpn-gateway/)
- 網站-Vpn: [https://azure.microsoft.com/documentation/articles/vpn-gateway-create-site-to-site-rm-powershell](./vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- ExpressRoute 文件 （請務必請參閱 「 開始 」 及 「 如何 」 區段）︰ [https://azure.microsoft.com/documentation/services/expressroute/](https://azure.microsoft.com/documentation/services/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "安全性選項] 流程圖"
[1]: ./media/best-practices-network-security/compliancebadges.png "Azure 規範徽章"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "Azure 安全性功能"
[3]: ./media/best-practices-network-security/dmzcorporate.png "在公司網路 DMZ"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "Azure 安全性架構"
[5]: ./media/best-practices-network-security/dmzazure.png "Azure 虛擬網路中 DMZ"
[6]: ./media/best-practices-network-security/dmzhybrid.png "使用三個安全性界限的混合式網路"
[7]: ./media/best-practices-network-security/example1design.png "輸入與 NSG DMZ"
[8]: ./media/best-practices-network-security/example2design.png "輸入的 DMZ NVA 與 NSG"
[9]: ./media/best-practices-network-security/example3design.png "雙向 DMZ NVA、 NSG，與 UDR"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "邏輯檢視的防火牆規則"
[11]: ./media/best-practices-network-security/example4designoptions.png "使用 NVA DMZ 連線混合式網路"
[12]: ./media/best-practices-network-security/example4designs2s.png "與使用網站-VPN 連線的 NVA DMZ"
[13]: ./media/best-practices-network-security/example4networklogical.png "邏輯網狀 NVA 觀點"
[14]: ./media/best-practices-network-security/example5designoptions.png "Azure 閘道器 DMZ 連線至網站混合式網路"
[15]: ./media/best-practices-network-security/example5designs2s.png "使用網站-VPN Azure 閘道器 DMZ"
[16]: ./media/best-practices-network-security/example6designoptions.png "Azure 閘道器 DMZ 連線 ExpressRoute 混合式網路"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "DMZ Azure 使用 ExpressRoute 連線的閘道器"

<!--Link References-->
[Example1]: ./virtual-network/virtual-networks-dmz-nsg-asm.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
