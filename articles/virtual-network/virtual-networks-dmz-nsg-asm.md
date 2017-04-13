<properties
   pageTitle="DMZ 範例 – 建立簡單的 NSGs DMZ |Microsoft Azure"
   description="建立網路安全性群組 (NSG) DMZ"
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
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-1--build-a-simple-dmz-with-nsgs"></a>範例 1︰ 建立簡單的 DMZ 與 NSGs

[返回 [安全性邊界最佳作法頁面][HOME]

此範例會建立簡單的 DMZ 與四個 windows 伺服器網路安全性群組。 它也會引導執行每個相關的命令，以提供更深入瞭解每個步驟。 也有提供您採取進階的流量分析藍本區段逐步流量經由防衛 DMZ 的圖層的方式。 最後，在參照區段是完整的程式碼，來建立此環境中測試並嘗試使用各種情況的指示。 

![輸入與 NSG DMZ][1]

## <a name="environment-description"></a>環境描述
在此範例中有訂閱包含下列動作︰

- 兩個雲端服務: 「 FrontEnd001 」 和 「 BackEnd001 」
- 虛擬網路，「 CorpNetwork 」，具有兩個子網路;「 FrontEnd 」 和 「 後端 」
- 套用至這兩個子網路安全性群組
- Windows 伺服器代表應用程式網頁伺服器 (「 IIS01 」)
- 代表回應用程式的兩個視窗伺服器端伺服器 （「 AppVM01 」、 「 AppVM02 」）
- Windows 伺服器代表 DNS 伺服器 (「 DNS01 」)

在下方的 [參考資料] 區段中有會建立大部分上述的環境的 PowerShell 指令碼。 建置 Vm 和虛擬網路，完成的範例指令碼，雖然不說明這份文件中的詳細資料。 

若要建立環境。

  1.    儲存包含在 [參考資料] 區段中的網路設定的 xml 檔案 （更新名稱、 位置及 IP 位址，以符合指定的情況）
  2.    更新的指令碼，以符合指令碼可帶 （訂閱、 服務名稱等） 的環境中的使用者變數
  3.    在 PowerShell 中執行指令碼

**附註**︰ 中的 PowerShell 指令碼表示的區域，必須符合表示網路設定的 xml 檔案中的區域。

一旦指令碼執行成功其他選用步驟可能需要採取，在 [參考資料] 區段中兩個指令碼的網頁伺服器和允許使用此 DMZ 設定測試的簡單的 web 應用程式的應用程式伺服器設定。

下列各節會提供詳細的描述的網路安全性群組以及其運作方式這個範例中的說明主要行的 PowerShell 指令碼。

## <a name="network-security-groups-nsg"></a>網路安全性群組 (NSG)
此範例中，NSG 群組是內建，然後再載入六個規則。 

>[AZURE.TIP] 一般而言，您應該先建立特定的 「 允許 」 規則首尾然後一般的 「 拒絕 」 規則。 指派的優先順序即表示規則會評估第一個。 一旦流量找到要套用至某特定規則時，會不評估任何進一步的規則。 NSG 規則可以套用中輸入或輸出方向 （從子網路的觀點）。

宣告方式，是輸入流量建置下列規則︰

1.  允許內部 DNS 流量 （連接埠 53）
2.  RDP （連接埠 3389） 從網際網路任何 vm 允許流量
3.  允許 HTTP （連接埠 80） 來自網際網路流量網頁伺服器 (IIS01)
4.  允許任何流量 （所有連接埠） IIS01 AppVM1
5.  任何 （所有連接埠） 來自網際網路流量為整個拒絕 VNet （兩個子網路）
6.  拒絕任何流量 （所有連接埠） Frontend 子網路的後端子網路

使用這些規則繫結至每一個子網路，如果 HTTP 要求輸入從網際網路網頁伺服器，而這兩種規則 3 （允許） 和 5 （拒絕） 會套用，但由於規則 3 具有較高的優先順序，只將套用的規則 5 就不會發生。 因此網頁伺服器就會允許 HTTP 要求。 如果連線 DNS01 伺服器嘗試相同流量，規則 5 （拒絕） 就會套用至第一個，並不想要允許流量傳送至伺服器。 規則 6 （拒絕） 封鎖從交談 （除了允許流量規則 1 到 4） 的後端子網路 Frontend 子網路，這可保護的大小寫的後端網路駭客侵入網頁上的應用程式的主選單中，而攻擊會有限制存取後端 」 受保護 」 的網路 （僅限公開 AppVM01 伺服器上的資源）。

有預設的輸出規則，允許出的網際網路流量。 例如，我們是允許外寄流量，並不修改任何輸出規則。 若要鎖定兩個方向的流量，使用者定義路由需要，這探索在 「 範例 3 」 下方。

每個規則，如下所示討論更多詳細資料 (**附註**︰ 中的任何項目下方開頭為貨幣符號中的清單 (例如︰ $NSGName) 從這份文件的 [參照] 區段中的指令碼的使用者定義變數):

1. 第一次必須建立網路安全性群組，如要保留的規則︰

        New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.  在此範例中的第一個規則可讓所有的內部網路的後端子網路上的 DNS 伺服器間的 DNS 流量。 此規則有一些重要的參數︰
  - [輸入] 代表流量的方向此規則就會生效。這是從子網路或虛擬機器的角度 （視而定，此 NSG 繫結）。 因此如果類型是 「 輸入 」 與流量輸入子網路、 會套用規則和離開子網路流量就不會受到此規則。
  - [優先順序] 設定會評估交通流量的順序。 更低的數字較高的優先順序。 一旦規則套用到特定的流量會不處理任何進一步的規則。 因此如果規則的優先順序 1 」 可讓流量，及規則的優先順序 2 拒絕流量，這兩種規則套用至流量然後流量就會允許流程 （因為規則 1 有較高的優先順序花費效果和套用任何進一步的規則）。
  - 「 動作 」 代表如果封鎖或允許受此規則的流量。

            Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.  此規則可讓 RDP 流量流向從網際網路 VNET 中 [子網路上的任何伺服器上的 RDP 連接埠。 此規則使用兩種選擇性地址前置字元。「 VIRTUAL_NETWORK 」 和 「 網際網路 」。 這是輕鬆地址的地址前置詞較大的類別。

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
            -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '3389' `
            -Protocol *

4.  此規則可讓您輸入的網際網路流量點擊網頁伺服器。 它不會變更路由的行為。只允許 IIS01 傳遞的流量。 因此從網際網路流量如果已為此規則會讓它並停止處理其他規則其目的地網頁伺服器。 （在規則的優先順序 140 所有其他輸入的網際網路流量遭到封鎖）。 如果您只處理 HTTP 流量，此規則無法進一步限制為僅允許目的地連接埠 80。

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
            -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] `
            -DestinationPortRange '*' `
            -Protocol *

5.  此規則可讓您將從 IIS01 伺服器 AppVM01 伺服器，後面的規則區塊後端流量的所有其他 Frontend 流量。 若要改善此規則，應該新增已知的連接埠。 例如，如果 IIS 伺服器正中只在 AppVM01 SQL Server，目的地連接埠範圍應該變更 」 * 」 （任何） 到允許較小的輸入的攻擊曲面圖上 AppVM01 應該 web 應用程式不會洩漏 1433 （SQL 連接埠）。

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
            -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
 
6.  此規則拒絕網路上的任何伺服器從網際網路流量。 結合優先順序 110 和 120 規則，可讓只輸入的網際網路流量到防火牆和其他伺服器和區塊 RDP 連接埠其他項目。 

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $VNetName VNet from the Internet" `
            -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '*' `
            -Protocol *
 
7.  最終的規則拒絕的後端子網路 Frontend 子網路流量。 這是輸入唯一的規則，因為 （從後端的主選單中） 允許反向流量。

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
            -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix `
            -DestinationPortRange '*' `
            -Protocol * 

## <a name="traffic-scenarios"></a>流量分析藍本
#### <a name="allowed-web-to-web-server"></a>（*允許*）Web 至網頁伺服器
1.  網際網路使用者要求 HTTP 頁面從 FrontEnd001.CloudApp.Net （網際網路對雲端服務）
2.  雲端服務傳送流量，透過開啟端點連接埠 80 IIS01 往上 （網頁伺服器）
3.  Frontend 子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    套用 NSG 規則 3 （網際網路 IIS01）、 流量是允許、 停止規則處理
4.  流量碰到內部 IP 位址的網頁伺服器 IIS01 (10.0.1.5)
5.  IIS01 網路流量接聽收到這項要求，然後開始處理要求
6.  IIS01 要求 SQL Server 上 AppVM01 資訊
7.  在主選單子網路上的沒有輸出規則，允許流量
8.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    NSG 規則 3 （網際網路的防火牆） 不會套用，移至下一個規則
  4.    NSG 規則 4 (IIS01 至 AppVM01) 並套用允許流量、 停止處理規則
9.  AppVM01 接收 SQL 查詢並回應
10. 由於後端子網路上沒有輸出規則允許回應
11. Frontend 子網路開始輸入的規則處理︰
  1.    沒有 NSG 規則套用至輸入 Frontend 子網路，所以無 NSG 的規則套用至後端子網路流量
  2.    預設系統規則，允許子網路之間的流量會允許此流量，因此會允許流量。
12. IIS 伺服器收到 SQL 回應完成 HTTP 回應和會傳送要求給
13. 因為有允許沒有輸出規則 Frontend 子網路上的回應，則與網際網路會收到要求的網頁。

#### <a name="allowed-rdp-to-backend"></a>（*允許*）若要後端 RDP
1.  在網際網路上的伺服器管理員要求上 BackEnd001.CloudApp.Net:xxxxx xxxxx 哪裡 RDP 至 AppVM01 （Azure 入口網站上，或透過 PowerShell，可找到指派的連接埠） 的隨機指定連接埠號碼 AppVM01 RDP 工作階段
2.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 會套用，流量允許、 停止規則處理
3.  沒有輸出規則]，套用預設規則與允許寄流量
4.  已啟用 RDP 工作階段
5.  AppVM01 會提示使用者名稱的密碼

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>（*允許*）DNS 伺服器上的網頁伺服器 DNS 查閱
1.  網頁伺服器、 IIS01、 需求資料摘要 www.data.gov，但若要解決地址的需求。
2.  網路設定 VNet 清單 DNS01 (10.0.2.4 後端子網路上) 為主要的 DNS 伺服器，IIS01 就會將 DNS 邀請傳送至 DNS01
3.  在主選單子網路上的沒有輸出規則，允許流量
4.  後端子網路開始輸入的規則處理︰
  1.     套用 NSG 規則 1 (DNS)，流量允許、 停止規則處理
5.  DNS 伺服器收到要求
6.  DNS 伺服器沒有快取的地址，並詢問在網際網路上的根 DNS 伺服器
7.  後端子網路上的沒有輸出規則，允許流量
8.  網際網路 DNS 伺服器回應，因為內部啟動此工作階段，會允許回應
9.  DNS 伺服器快取的回應，並回到 IIS01 初始要求回應
10. 後端子網路上的沒有輸出規則，允許流量
11. Frontend 子網路開始輸入的規則處理︰
  1.    沒有 NSG 規則套用至輸入 Frontend 子網路，所以無 NSG 的規則套用至後端子網路流量
  2.    預設系統規則，允許子網路之間的流量會允許此流量，因此會允許流量
12. IIS01 從 DNS01 接收回應

#### <a name="allowed-web-server-access-file-on-appvm01"></a>（*允許*）在 AppVM01 上的網頁伺服器 access 檔案
1.  IIS01 要求 AppVM01 上的檔案
2.  在主選單子網路上的沒有輸出規則，允許流量
3.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    NSG 規則 3 （網際網路 IIS01） 不會套用，移至下一個規則
  4.    NSG 規則 4 (IIS01 至 AppVM01) 並套用允許流量、 停止處理規則
4.  AppVM01 收到要求與回應檔案 （假設有權存取）
5.  由於後端子網路上沒有輸出規則允許回應
6.  Frontend 子網路開始輸入的規則處理︰
  1.    沒有 NSG 規則套用至輸入 Frontend 子網路，所以無 NSG 的規則套用至後端子網路流量
  2.    預設系統規則，允許子網路之間的流量會允許此流量，因此會允許流量。
7.  IIS 伺服器接收檔案

#### <a name="denied-web-to-backend-server"></a>（*拒絕*）後端伺服器網頁
1.  網際網路使用者嘗試透過 BackEnd001.CloudApp.Net 服務存取 AppVM01 上的檔案
2.  沒有開啟的檔案共用的端點，因為這不會傳送雲端服務，並可連到伺服器
3.  如果基於某種原因開啟端點，NSG 規則 5 （網際網路 VNet） 封鎖流量

#### <a name="denied-web-dns-lookup-on-dns-server"></a>（*拒絕*）在 [DNS 伺服器上的 web DNS 查閱
1.  網際網路使用者嘗試查閱的內部 DNS 記錄上 DNS01 BackEnd001.CloudApp.Net 服務
2.  沒有開啟 dns 的端點，因為這不會傳送雲端服務，並可連到伺服器
3.  如果基於某種原因開啟端點，NSG 規則 5 （網際網路 VNet） 封鎖流量 (請注意︰ 該規則 1 (DNS) 不會套用有兩個原因，第一次來源地址是網際網路、 這個規則只套用到本機的 VNet 做為來源，也這是允許規則，讓它會永遠不會拒絕流量)

#### <a name="denied-web-to-sql-access-through-firewall"></a>（*拒絕*）透過防火牆 SQL 存取的網站
1.  網際網路使用者要求 SQL 資料從 FrontEnd001.CloudApp.Net （網際網路對雲端服務）
2.  沒有開啟 SQL 的端點，因為這不會傳送雲端服務，並不會達到防火牆
3.  如果基於某種原因開啟端點，Frontend 子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    套用 NSG 規則 3 （網際網路 IIS01）、 流量是允許、 停止規則處理
4.  流量碰到 IIS01 內部 IP 位址 (10.0.1.5)
5.  IIS01 未接聽連接埠 1433，因此沒有回應的要求

## <a name="conclusion"></a>結論
這是相當簡單且直接轉寄的方式，隔離從輸入流量的後端子網路。

更多範例，以及網路安全性界限的概觀，請參閱[以下][HOME]。

## <a name="references"></a>參照
### <a name="main-script-and-network-config"></a>主要指令碼和網路設定
PowerShell 指令碼檔儲存完整的指令碼。 將網路設定儲存成一個名為 「 NetworkConf1.xml 」 檔案。
如有需要請修改使用者定義的變數。 執行指令碼，然後依照上述範例 1 一節中所包含的防火牆規則安裝指示。

#### <a name="full-script"></a>完整的指令碼
這個指令碼，將會根據使用者定義的變數。

1.  連線至 Azure 的訂閱
2.  建立新的儲存空間帳戶
3.  建立新的 VNet 和兩個子網路，網路設定檔中的定義
4.  建立 4 的 windows server Vm
5.  設定 NSG 包括︰
  - 建立 NSG
  - 填入規則
  - 繫結至適當的子網路的 NSG

這個 PowerShell 指令碼應執行在本機電腦或伺服器連線到網際網路。

>[AZURE.IMPORTANT] 執行這個指令碼時，可能會有警告或彈入 PowerShell 其他資訊訊息。 僅限的錯誤訊息，以紅色都必須注意的問題。


    <# 
     .SYNOPSIS
      Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
      
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 0
        #       - The AppVM1 Server must be VM 1
        #       - The DNS server must be VM 3
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 1 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 2 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 3 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #   

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                Remove-AzureEndpoint -Name "PowerShell" | `
                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
            $i++
        }
        # Add HTTP Endpoint for IIS01
        Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
      

#### <a name="network-config-file"></a>網路設定檔
更新的位置以儲存此 xml 檔案，為此檔案中的指令碼上述 $NetworkConfigFile 變數新增連結。
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>指令碼範例應用程式
如果想要安裝範例應用程式，與其他 DMZ 範例，請在下列連結將其中一個提供︰[範例應用程式指令碼][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "輸入與 NSG DMZ"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

