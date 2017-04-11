<properties
   pageTitle="實作與 Azure 及內部部署 VPN 的混合式網路架構 |Microsoft Azure"
   description="如何實作橫跨 Azure 虛擬網路和使用 VPN 連線內部網路的安全網站間的網路架構。"
   services=""
   documentationCenter="na"
   authors="RohitSharma-pnp"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="roshar"/>

# <a name="implementing-a-hybrid-network-architecture-with-azure-and-on-premises-vpn"></a>實作與 Azure 及內部部署 VPN 的混合式網路架構

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

本文概述作法延伸到 Azure 網站-虛擬私人網路 (VPN) 使用內部部署網路的設定。 流量流動內部網路和 IPSec VPN 通道透過 Azure 虛擬網路 (VNet)。 此結構是適用於下列特性的混合式應用程式︰

- 當其他人 Azure 中執行時，應用程式組件執行內部部署。

- 之間的流量內部部署的硬體和雲端很可能是 light，或有利貿易彈性和處理 power 雲端的位置略有延伸的延遲。

- 擴充的網路構成關閉的系統。 沒有直接從網際網路路徑至 Azure VNet。

- 使用者連線到內部部署網路，可使用裝載於 Azure 服務。 內部網路和 Azure 中執行服務橋樑是透明的使用者。

配合這個設定檔的情況的範例包括︰

- 線條的商務應用程式中組織中，使用部分功能已移轉至雲端。

- 開發/測試實驗室負載。

> [AZURE.NOTE] Azure 有兩種不同的部署模型︰ [Azure 資源管理員][resource-manager-overview]和傳統。 這個藍圖使用資源管理員，Microsoft 建議新的部署。

## <a name="architecture-diagram"></a>架構圖表

下圖會醒目提示在這種架構元件︰

> Visio 文件包含此架構圖是可從[Microsoft 下載中心][visio-download]。 這是 「 混合式網路上-VPN 」] 頁面。

![[0]][0]

- **內部部署網路。** 這是一個網路的電腦和裝置，透過私人執行在組織內的區域網路連線。

- **VPN 應用裝置。** 這是在裝置或服務，提供外部內部部署網路連線能力。 VPN 應用裝置可能硬體裝置，或在 Windows Server 2012 中的遠端存取服務 (RRAS) 路由等軟體解決方案。

> [AZURE.NOTE] 如需支援的 VPN 就和設定選取的 VPN 就需連線至 Azure VPN 閘道器的資訊的清單，請參閱[支援 Azure VPN 裝置清單]中適當的裝置的指示[vpn-appliance]。

- **N 層雲端應用程式。** 這是裝載於 Azure 的應用程式。 它可以包含多個層級，具有多個子網路連線透過 Azure 負載平衡器。 在每一個子網路流量可能會使用[Azure 網路安全性群組 (NSGs)]來定義規則[azure-network-security-group]。 如需詳細資訊，請參閱[快速入門 Microsoft Azure 安全性][getting-started-with-azure-security]。

> [AZURE.NOTE] 本文將說明雲端應用程式為單一項目。 請參閱[執行在 Azure N 層架構][implementing-a-multi-tier-architecture-on-Azure]的詳細資訊。

- **[虛擬網路 (VNet)][azure-virtual-network]。** 雲端應用程式和 Azure VPN 閘道器的元件位於相同的 VNet。

- **[Azure VPN 閘道][azure-vpn-gateway]。** VPN 閘道器服務可讓您透過 VPN 應用裝置連線至內部網路的 VNet。 如需詳細資訊，請參閱[連線到 Microsoft Azure 虛擬網路內部部署網路][connect-to-an-Azure-vnet]。 VPN 閘道器會包含下列元素︰

  - **虛擬網路閘道器。** 這是虛擬的 VPN 應用裝置提供 VNet 資源。 它會負責路由流量從內部部署網路至 VNet。

  - **區域網路閘道器。** 這是內部部署 VPN 應用裝置的抽象。 從內部部署網路雲端應用程式的網路流量被經由此閘道器。

  - **連線。** 連線的屬性會指定連線類型 (IPSec)，以及共用與內部部署 VPN 應用裝置的鍵加密流量。

  - **閘道器子網路。** 虛擬網路閘道器會保留在其本身子網路] 下的 [建議] 區段中所述受到各種不同的需求。

- **內部負載平衡器。** 從 VPN 閘道器的網路流量路由至內部負載平衡器透過雲端應用程式。 負載平衡器位於應用程式的前端子網路。

## <a name="recommendations"></a>建議

Azure 提供許多不同的資源和資源類型，因此此參照架構可提供許多不同的方式。 我們提供安裝遵循這些建議參考架構 Azure 資源管理員範本。 如果您選擇，建立您自己參考架構，您應該遵循上述建議，除非您有建議不符合特定需求。

### <a name="vnet-and-gateway-subnet"></a>VNet 和閘道的子網路

建立 Azure VNet 保留在雲端的元件的。 Azure VNet 的位址空間必須夠大，無法容納 Vm 和子網路 VNet 中的所使用的地址。 確定 VNet 位址空間可能會在未來需要額外 Vm 是否有足夠的成長的空間。 不可以與內部部署網路重疊 VNet 的位址空間。 例如，圖會使用地址空間 10.20.0.0/16 VNet。

建立以 /27 地址範圍命名_GatewaySubnet_，子網路。 子網路所需的虛擬網路閘道器，並配置此子網路 32 地址會防止您在未來執行進而可能的閘道器的大小限制。 避免將子網路中間位址空間。 考慮是將地址的空間的閘道器子網路設定結尾上方的 VNet 位址空間。 在圖表中所示的範例使用 10.20.255.224/27。  若要計算 CIDR 快速程序如下所示︰

1. 變數位在中設定為 1，最多正在使用的閘道器子網路中，然後設定為 0 剩餘的位元的位元 VNet 的位址空間。

2. 將產生的位元轉換成十進位和 express 將其作為前置詞長度設定大小的閘道器子網路位址空間。

例如，用的 IP 位址範圍的 10.20.0.0/16 VNet，套用上面的步驟 1 會變成 10.20.0b11111111.0b11100000。  將的轉換成十進位和表示它為地址空間產生 10.20.255.224/27

> [AZURE.WARNING] 不要到閘道器的子網路部署其他虛擬機器或角色執行個體。 此外，沒有指派 NSG 子網路，因為這會降低閘道器至停止運作。

### <a name="virtual-network-gateway"></a>虛擬網路閘道器

虛擬網路閘道器的配置的公用 IP 位址。

閘道器子網路中建立虛擬網路閘道器，並將它指派新配置的公用 IP 位址。 使用最符合您需求的閘道器類型，其中會啟用您要有 vpn 才能應用裝置︰

建立[原則為基礎的閘道器][policy-based-routing]如果您需要密切控制要求路由的方式。 根據原則準則，例如 [地址前置字元。 原則為基礎的閘道器會使用 [靜態路由]，並僅適用於用網站的連線。

建立[路由為基礎的閘道器][route-based-routing]如果您連線到內部部署網路使用 RRAS，支援多重網站或跨區域的連線，或實作 VNet-VNet 連線 （包括路由，通過多個 VNets）。 路由為基礎的閘道器會使用動態路由至網路之間的直接流量。 可以容許失敗網路路徑更多靜態路由，因為他們可以嘗試替代路由。 路由為基礎的閘道器也可以縮減管理費用，因為路由可能不需要的網路位址變更時，以手動方式更新。

如需支援 VPN 裝置清單，請參閱[關於 VPN 裝置連線至網站 VPN 閘道器的][vpn-appliances]。

> [AZURE.NOTE] 在建立閘道器之後，您無法變更閘道器類型，但不刪除和重新建立閘道器之間。

選取最符合您處理量需求 Azure VPN 閘道器 SKU。 在下表所示的三個 sku 皆可使用 azure VPN 閘道器。 每個 SKU 提供不同的處理量，[費用 levied] [azure-gateway-charges]根據的閘道器已佈建的時間和使用。

| SKU | VPN 處理量 | 最大值 IPSec 通道|
|-----|----------------|------------------|
| 基本 | 100 Mbps | 10 |
| 標準 | 100 Mbps | 10 |
| 高效能 | 200 Mbps | 30 |

> [AZURE.NOTE] 基本 SKU 不相容的 Azure ExpressRoute。 您可以[變更 SKU] [changing-SKUs]建立閘道器之後。

建立閘道器至內部負載平衡器，而非允許直接傳遞至 Vm 實作應用程式要求的閘道器子網路路由規則，直接連入應用程式流量。

### <a name="on-premises-network-connection"></a>內部部署網路連線

建立區域網路閘道器。 指定內部部署 VPN 應用裝置和內部部署網路的位址空間的公用 IP 位址。 請注意，內部部署 VPN 應用裝置必須 Azure VPN 閘道器的區域網路閘道器可以存取的公用 IP 位址。 NAT 裝置後面的找不到的 VPN 裝置。

建立虛擬網路閘道器與本機網路閘道器的網站的連線。 選取網站的網站 」 (IPSec) 連線類型，並指定 [共用] 鍵。 網站-加密 Azure VPN 閘道器會根據 IPSec 通訊協定，使用預先共用的金鑰進行驗證。 當您建立 Azure VPN 閘道器時，您可以指定索引鍵。 您必須設定 VPN 應用裝置執行相同的金鑰內部部署。 目前不支援其他驗證機制。

請確認內部部署路由基礎結構會設定為供 Azure VNet VPN 裝置中的地址的轉寄要求。

開啟任何內部網路中的雲端應用程式所需的連接埠。

測試連線]，確認︰

- 內部部署 VPN 應用裝置正確路由流量至雲端應用程式，透過 Azure VPN 閘道器。

- VNet 正確路由流量至內部部署網路。

- 禁止兩個方向會封鎖流量，正確。

## <a name="scalability-considerations"></a>延展性考量

您可以從基本或標準 VPN 閘道器 sku 皆可移至高的效能 VPN SKU 達成有限的垂直擴充。

如需預期大量 VPN 流量的 VNets，請考慮分散到另一個較小的 VNets 不同的工作負載和 VPN 閘道器設定的每個。

您可以在水平或垂直分割 VNet。 若要水平分割，將某些 VM 執行個體中每一層到的新 VNet 子網路。 結果為每個 VNet 具有相同的結構和功能。 若要垂直分割，重新設計每一層，將分成不同邏輯的區域 （例如處理訂單、 發票、 客戶管理，等等） 的功能。 每個功能的區域然後放置在它自己 VNet。

複製 VNet，內部部署 Active Directory 網域控制站及實作 DNS VNet，可協助降低部分的安全性與管理流量來自內部部署至雲端。

## <a name="availability-considerations"></a>可用性考量

若要確保內部部署網路才能 Azure VPN 閘道器，實作容錯移轉叢集內部部署 VPN 閘道器。

如果您的組織擁有多個內部部署網站，建立[多網站連線][vpn-gateway-multi-site]至一或多個 Azure VNets。 這種方法可讓您需要動態 （路由型） 路由，因此請確定 [內部部署 VPN 閘道支援此功能。

請參閱[VPN 閘道器的 SLA] [ sla-for-vpn-gateway] VPN 閘道器 SLA 的相關詳細資料。

## <a name="manageability-considerations"></a>管理能力考量

監控從內部部署 VPN 設備的診斷資訊。 此程序取決於所要有 vpn 才能應用裝置提供的功能。 例如，如果您正在使用 [路由及遠端存取服務，在 Windows Server 2012，您應該啟用[RRAS 記錄][rras-logging]。

使用[Azure VPN 閘道診斷][gateway-diagnostic-logs]擷取連線問題的相關資訊。 這些記錄可以用於追蹤資訊，例如來源與目的地連線要求、 通訊所使用，以及如何建立連線 （或嘗試失敗的原因）。

使用 Azure 入口網站中可用的稽核記錄監控 Azure VPN 閘道器的作業的記錄。 個別的記錄檔，可用於區域網路閘道器與 Azure 網路閘道器連線。 這項資訊可用於追蹤閘道器，對所做的任何變更，會有幫助如果先前正常運作的閘道器基於某種原因停止運作。

![[2]][2]

監控連線，並追蹤連線失敗事件。 您可以使用監控套件，例如[Nagios] [nagios]來擷取與報表這項資訊。

## <a name="security-considerations"></a>安全性考量

產生不同共用每個 VPN 閘道器金鑰。 使用強式的共用的鍵可協助抵禦惡意攻擊。

> [AZURE.NOTE] 目前，您便無法使用的預先共用的金鑰 Azure VPN 閘道 Azure 金鑰保存庫。

確保內部部署 VPN 應用裝置使用加密方法相容[於 Azure VPN 閘道][vpn-appliance-ipsec]。 如需原則型路由]，Azure VPN 閘道器會支援 AES256 AES128，與 3DES 加密演算法。 路由為基礎的閘道器支援 AES256 及 3DES。

如果您的內部部署 VPN 應用裝置位於周邊網路有防火牆周邊網路和網際網路之間，您可能要設定[其他防火牆規則][additional-firewall-rules]允許網站-VPN 連線。

如果應用程式以 VNet 傳送資料至網際網路，請考慮[實作強制通道][forced-tunneling]若要傳送所有的網際網路繫結流量，經由內部網路。 這種方法可讓您稽核所做的應用程式從內部部署基礎結構的外寄要求。

> [AZURE.NOTE] 強制通道會影響連線至 Azure 服務 （儲存服務，例如） 和 Windows 授權管理員。

## <a name="troubleshooting-considerations"></a>疑難排解考量

> [AZURE.NOTE] 請造訪路由和遠端存取部落格的一般資訊[VPN 相關的常見錯誤]的疑難排解步驟[troubleshooting-vpn-errors]。

如果流量無法通過 VPN 連線，請檢查以下項目︰

### <a name="on-premises-vpn-appliance"></a>內部部署 VPN 應用裝置︰

**內部部署 VPN 應用裝置正常運作？**

核取任何記錄產生的錯誤和失敗 VPN 應用裝置的檔案。 這項資訊的位置會根據您的應用裝置而有所不同。 例如，如果您使用的在 Windows Server 2012 RRAS，您可以使用下列 PowerShell 命令顯示錯誤 RRAS 服務事件資訊︰

```
Get-EventLog -LogName System -EntryType Error -Source RemoteAccess | Format-List -Property *
```

[_郵件_] 屬性的每一個項目提供錯誤的說明。 以下是一些常見範例︰

- 無法連線，可能是因為錯誤 Azure VPN 閘道器 RRAS VPN 網路介面設定中指定的 IP 位址︰

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {41, 3, 0, 0}
Index              : 14231
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: The network connection between your computer and
                     the VPN server could not be established because the remote server is not responding. This could
                     be because one of the network devices (e.g, firewalls, NAT, routers, etc) between your computer
                     and the remote server is not configured to allow VPN connections. Please contact your
                     Administrator or your service provider to determine which device may be causing the problem.
Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, The network connection between
                     your computer and the VPN server could not be established because the remote server is not
                     responding. This could be because one of the network devices (e.g, firewalls, NAT, routers, etc)
                     between your computer and the remote server is not configured to allow VPN connections. Please
                     contact your Administrator or your service provider to determine which device may be causing the
                     problem.}
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:26:02 PM
TimeWritten        : 3/18/2016 1:26:02 PM
UserName           :
Site               :
Container          :
```

- RRAS VPN 網路介面設定中所指定的錯誤共用機碼︰

```
EventID            : 20111
MachineName        : on-prem-vm
Data               : {233, 53, 0, 0}
Index              : 14245
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : RoutingDomainID- {00000000-0000-0000-0000-000000000000}: A Demand Dial connection to the remote
                     interface AzureGateway on port VPN2-4 was successfully initiated but failed to complete
                     successfully because of the  following error: IKE authentication credentials are unacceptable

Source             : RemoteAccess
ReplacementStrings : {{00000000-0000-0000-0000-000000000000}, AzureGateway, VPN2-4, IKE authentication credentials are
                     unacceptable
                     }
InstanceId         : 20111
TimeGenerated      : 3/18/2016 1:34:22 PM
TimeWritten        : 3/18/2016 1:34:22 PM
UserName           :
Site               :
Container          :
```

您也可以取得嘗試使用下列 PowerShell 命令連線到 RRAS 服務相關的事件記錄檔資訊︰

```
Get-EventLog -LogName Application -Source RasClient | Format-List -Property *
```

連線失敗，此記錄會包含看起來類似下列錯誤︰

```
EventID            : 20227
MachineName        : on-prem-vm
Data               : {}
Index              : 4203
Category           : (0)
CategoryNumber     : 0
EntryType          : Error
Message            : CoId={B4000371-A67F-452F-AA4C-3125AA9CFC78}: The user SYSTEM dialed a connection named
                     AzureGateway which has failed. The error code returned on failure is 809.
Source             : RasClient
ReplacementStrings : {{B4000371-A67F-452F-AA4C-3125AA9CFC78}, SYSTEM, AzureGateway, 809}
InstanceId         : 20227
TimeGenerated      : 3/18/2016 1:29:21 PM
TimeWritten        : 3/18/2016 1:29:21 PM
UserName           :
Site               :
Container          :
```

**透過 Azure VPN 閘道是 VPN 應用裝置正確路由流量？**

使用的工具，例如[PsPing] [psping]驗證連線與路由跨 VPN 閘道器。 例如，若要測試從內部部署電腦位於 VNet 的網頁伺服器的連線，請執行下列命令 (取代`<<web-server-address>>`的網頁伺服器的位址):

```
PsPing -t <<web-server-address>>:80
```

如果內部部署電腦可以將流量路由傳送至網頁伺服器，您應該會看到類似以下的輸出︰

```
D:\PSTools>psping -t 10.20.0.5:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.0.5:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.0.5:80 (warmup): 6.21ms
Connecting to 10.20.0.5:80: 3.79ms
Connecting to 10.20.0.5:80: 3.44ms
Connecting to 10.20.0.5:80: 4.81ms

  Sent = 3, Received = 3, Lost = 0 (0% loss),
  Minimum = 3.44ms, Maximum = 4.81ms, Average = 4.01ms
```

如果內部部署電腦無法與指定的目的，您會看到如下的訊息︰

```
D:\PSTools>psping -t 10.20.1.6:80

PsPing v2.01 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2014 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.20.1.6:80:
Infinite iterations (warmup 1) connecting test:
Connecting to 10.20.1.6:80 (warmup): This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80: This operation returned because the timeout period expired.
Connecting to 10.20.1.6:80:
  Sent = 3, Received = 0, Lost = 3 (100% loss),
  Minimum = 0.00ms, Maximum = 0.00ms, Average = 0.00ms
```

**內部防火牆是否允許 VPN 流量通過？已開啟正確的連接埠嗎？**

請確認內部部署 VPN 應用裝置使用加密方法相容[於 Azure VPN 閘道][vpn-appliance]。

如需原則型路由]，Azure VPN 閘道器會支援 AES256 AES128，與 3DES 加密演算法。 路由為基礎的閘道器支援 AES256 及 3DES。

### <a name="azure-vnet-gateway"></a>Azure VNet 閘道︰

檢查[Azure VPN 閘道診斷記錄][gateway-diagnostic-logs]潛在問題。

**Azure VPN 閘道器與內部部署 VPN 應用裝置設定以相同的共用的驗證金鑰是？**

您可以檢視共用的金鑰儲存 Azure VPN 閘道器，使用下列 Azure CLI 命令︰

```
azure network vpn-connection shared-key show <<resource-group>> <<vpn-connection-name>>
```

若要顯示的應用裝置設定的共用索引鍵使用適用於您的內部部署 VPN 應用裝置的命令。

請確認拿著 Azure VPN 閘道器無法與 NSG 相關聯的_GatewaySubnet_子網路。

您可以使用下列 Azure CLI] 命令來檢視子網路詳細資料︰

```
azure network vnet subnet show -g <<resource-group>> -e <<vnet-name>> -n GatewaySubnet
```

確認那里無資料欄位名稱為_網路安全性群組識別碼_。 下列範例顯示的已指派的 NSG （_VPN 閘道群組_） _GatewaySubnet_執行個體的結果。 這可能會導致防止閘道器無法正常運作，如果有任何此 NSG 定義的規則︰

```
C:\>azure network vnet subnet show -g profx-prod-rg -e profx-vnet -n GatewaySubnet
    info:    Executing command network vnet subnet show
    + Looking up virtual network "profx-vnet"
    + Looking up the subnet "GatewaySubnet"
    data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/virtualNetworks/profx-vnet/subnets/GatewaySubnet
    data:    Name                            : GatewaySubnet
    data:    Provisioning state              : Succeeded
    data:    Address prefix                  : 10.20.3.0/27
    data:    Network Security Group id       : /subscriptions/########-####-####-####-############/resourceGroups/profx-prod-rg/providers/Microsoft.Network/networkSecurityGroups/VPN-Gateway-Group
    info:    network vnet subnet show command OK
```

**設定為允許來自外部 VNet 流量虛擬機器中 Azure VNet 嗎？核取 [包含這些虛擬機器子網路相關聯的任何 NSG 規則]。**

您可以使用下列 Azure CLI] 命令來檢視所有 NSG 規則︰

```
azure network nsg show -g <<resource-group>> -n <<nsg-name>>
```

**已中斷 Azure VPN 閘道器？**

若要查看目前狀態的 Azure VPN 連線，您可以使用下列 PowerShell 的 Azure 命令。 `<<connection-name>>`參數是連結虛擬網路閘道器與本機閘道 Azure VPN 連線的名稱。

```
Get-AzureRmVirtualNetworkGatewayConnection -Name <<connection-name>> - ResourceGroupName <<resource-group>>
```

下列程式碼片段醒目提示產生如果閘道連線 （第一個範例），並中斷連線的輸出 （第二個範例）︰

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : Connected
EgressBytesTransferred     : 55254803
IngressBytesTransferred    : 32227221
ProvisioningState          : Succeeded
...
```

```
PS C:\> Get-AzureRmVirtualNetworkGatewayConnection -Name profx-gateway-connection2 -ResourceGroupName profx-prod-rg

AuthorizationKey           :
VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
VirtualNetworkGateway2     :
LocalNetworkGateway2       : Microsoft.Azure.Commands.Network.Models.PSLocalNetworkGateway
Peer                       :
ConnectionType             : IPsec
RoutingWeight              : 0
SharedKey                  : ####################################
ConnectionStatus           : NotConnected
EgressBytesTransferred     : 0
IngressBytesTransferred    : 0
ProvisioningState          : Succeeded
...
```

### <a name="host-vm-configuration-network-bandwidth-utilization-and-application-performance"></a>Host （主機） VM 設定網路頻寬使用量，與應用程式的效能︰

請確認 Azure Vm 在子網路上執行來賓作業系統中的防火牆正確設定為允許從內部部署 IP 範圍允許的流量。

**提供的流量的頻寬限制靠近 Azure VPN 閘道？**

工具取決於您執行的內部部署的 VPN 應用裝置使用的設備。 例如，如果您使用的在 Windows Server 2012 RRAS，可以使用效能監視器追蹤資料所接收，並透過 VPN 連線; 傳輸的音量使用_RAS 總_的物件，請選取_每秒位元組接收_及_傳送秒的位元組_計數器︰

![[3]][3]

您應該比較的結果與頻寬才能 VPN 閘道器 （100 Mbps 的基本版和標準的 Sku 及 200 Mbps 高的效能 SKU 版）︰

![[4]][4]

**Azure VNet 中的虛擬機器中的任何執行速度緩慢？他們超載的是，有足夠這些處理負載了所有負載-平衡器正確設定？**

您必須[擷取及分析的診斷資訊][azure-vm-diagnostics]。 您可以檢查結果使用 Azure 入口網站，但是許多協力廠商工具也提供的可提供效能資料的細節。

**是進行有效率的應用程式使用雲端資源？**

若要判斷是否應用程式進行資源的最佳使用每個 VM 上執行樂器應用程式碼。 您可以使用[應用程式的深入見解]等工具[application-insights]可協助執行這些工作。

## <a name="solution-deployment"></a>解決方案部署

如果您有已 VPN 應用裝置使用現有的內部部署基礎結構時，您可以部署參考架構，遵循下列步驟︰

1. 以滑鼠右鍵按一下 [下] 按鈕，然後選取 [」 開啟連結新索引標籤中的 」 或 「 新視窗中的開啟連結 」:  
[![部署至 Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn%2Fazuredeploy.json)

2. 等待連結来開啟在 Azure 入口網站，然後遵循下列步驟︰ 
    - **資源群組**名稱已經定義參數檔案中，**建立新檔案**，然後選取輸入`ra-hybrid-vpn-rg`在文字方塊中。
    - 從 [**位置**] 下拉式方塊中選取的地區。
    - 無法編輯**範本根 Uri**或**參數根 Uri**的 [文字] 方塊。
    - 檢閱條款與條件，然後按一下 [**我同意條款與條件上述**] 核取方塊。
    - 按一下 [**購買**] 按鈕。

3. 等到完成部署。

## <a name="next-steps"></a>後續步驟

- [安裝 Azure VNet 中使用 Vm 內部部署 Active Directory 網域中的其他網域控制站][installing-ad]。

- [部署上 Azure Vm Windows Server Active Directory 的指導方針][deploying-ad]。

- [建立的 DNS 伺服器中 VNet][creating-dns]。

- [設定及管理 DNS 中 VNet][configuring-dns]。

- [透過 VPN 使用內部部署 Stormshield 網路安全性應用裝置][stormshield]。

- [實作與 Azure ExpressRoute 的混合式網路架構][expressroute]。

<!-- links -->

[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-n-tier-vm.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[arm-templates]: ../resource-group-authoring-templates.md
[azure-cli]: ../virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-virtual-network]: ../virtual-network/virtual-networks-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: https://azure.microsoft.com/services/vpn-gateway/
[azure-gateway-charges]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[azure-network-security-group]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[vpn-gateway-multi-site]: ../vpn-gateway/vpn-gateway-multi-site.md
[policy-based-routing]: https://en.wikipedia.org/wiki/Policy-based_routing
[route-based-routing]: https://en.wikipedia.org/wiki/Static_routing
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[sla-for-vpn-gateway]: https://azure.microsoft.com/support/legal/sla/vpn-gateway/v1_0/
[additional-firewall-rules]: https://technet.microsoft.com/library/dn786406.aspx#firewall
[nagios]: https://www.nagios.org/
[azure-vpn-gateway-diagnostics]: http://blogs.technet.com/b/keithmayer/archive/2014/12/18/diagnose-azure-virtual-network-vpn-connectivity-issues-with-powershell.aspx
[ping]: https://technet.microsoft.com/library/ff961503.aspx
[tracert]: https://technet.microsoft.com/library/ff961507.aspx
[psping]: http://technet.microsoft.com/sysinternals/jj729731.aspx
[nmap]: http://nmap.org
[changing-SKUs]: https://azure.microsoft.com/blog/azure-virtual-network-gateway-improvements/
[gateway-diagnostic-logs]: http://blogs.technet.com/b/keithmayer/archive/2015/12/07/step-by-step-capturing-azure-resource-manager-arm-vnet-gateway-diagnostic-logs.aspx
[troubleshooting-vpn-errors]: https://blogs.technet.microsoft.com/rrasblog/2009/08/12/troubleshooting-common-vpn-related-errors/
[rras-logging]: https://www.petri.com/enable-diagnostic-logging-in-windows-server-2012-r2-routing-and-remote-access
[create-on-prem-network]: https://technet.microsoft.com/library/dn786406.aspx#routing
[create-azure-vnet]: ../virtual-network/virtual-networks-create-vnet-classic-cli.md
[azure-vm-diagnostics]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[application-insights]: ../application-insights/app-insights-overview-usage.md
[forced-tunneling]: https://azure.microsoft.com/documentation/articles/vpn-gateway-about-forced-tunneling/
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[vpn-appliances]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[installing-ad]: ../active-directory/active-directory-install-replica-active-directory-domain-controller.md
[deploying-ad]: https://msdn.microsoft.com/library/azure/jj156090.aspx
[creating-dns]: https://blogs.msdn.microsoft.com/mcsuksoldev/2014/03/04/creating-a-dns-server-in-azure-iaas/
[configuring-dns]: ../virtual-network/virtual-networks-manage-dns-in-vnet.md
[stormshield]: https://azure.microsoft.com/marketplace/partners/stormshield/stormshield-network-security-for-cloud/
[vpn-appliance-ipsec]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec-parameters
[expressroute]: ./guidance-hybrid-network-expressroute.md
[naming conventions]: ./guidance-naming-conventions.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/deploy-reference-architecture.sh
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetwork.parameters.json
[virtualNetworkGateway-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn/parameters/virtualNetworkGateway.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[0]: ./media/blueprints/hybrid-network-vpn.png "混合式的結構網路橫跨內部部署與雲端基礎結構"
[1]: ./media/guidance-hybrid-network-vpn/partitioned-vpn.png "分割 VNet 改善延展性"
[2]: ./media/guidance-hybrid-network-vpn/audit-logs.png "Azure 入口網站中的稽核記錄"
[3]: ./media/guidance-hybrid-network-vpn/RRAS-perf-counters.png "監視 VPN 網路流量的效能計數器"
[4]: ./media/guidance-hybrid-network-vpn/RRAS-perf-graph.png "範例 VPN 網路效能圖形"