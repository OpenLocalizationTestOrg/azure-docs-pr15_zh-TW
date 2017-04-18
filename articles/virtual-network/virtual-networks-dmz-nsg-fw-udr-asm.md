<properties
   pageTitle="DMZ 範例 – 建立以保護網路使用防火牆、 UDR 及 NSG DMZ |Microsoft Azure"
   description="建立具有防火牆 DMZ、 使用者定義路由 (UDR) 和網路安全性群組 (NSG)"
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

# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>範例 3-建立以保護網路使用防火牆、 UDR 及 NSG DMZ

[返回 [安全性邊界最佳作法頁面][HOME]

此範例會建立 DMZ 與防火牆、 四個 windows 伺服器、 使用者定義路由、 IP 轉接，網路安全性群組。 它也會引導執行每個相關的命令，以提供更深入瞭解每個步驟。 也有提供您採取進階的流量分析藍本區段逐步流量經由防衛 DMZ 的圖層的方式。 最後，在參照區段是完整的程式碼，來建立此環境中測試並嘗試使用各種情況的指示。 

![雙向 DMZ NVA、 NSG，與 UDR][1]

## <a name="environment-setup"></a>環境設定
在此範例中有訂閱包含下列動作︰

- 三個雲端服務: 「 SecSvc001 」、 「 FrontEnd001 」，以及 「 BackEnd001 」
- 虛擬網路 「 CorpNetwork 」，具有三個子網路: 「 SecNet 」、 「 FrontEnd 」，以及 「 後端 」
- 網路的虛擬應用，在此範例中防火牆來說，連線到 SecNet 子網路
- Windows 伺服器代表應用程式網頁伺服器 (「 IIS01 」)
- 代表回應用程式的兩個視窗伺服器端伺服器 （「 AppVM01 」、 「 AppVM02 」）
- Windows 伺服器代表 DNS 伺服器 (「 DNS01 」)

在下方的 [參考資料] 區段中有會建立大部分上述的環境的 PowerShell 指令碼。 建置 Vm 和虛擬網路，完成的範例指令碼，雖然不說明這份文件中的詳細資料。

若要建立環境︰

  1.    儲存包含在 [參考資料] 區段中的網路設定的 xml 檔案 （更新名稱、 位置及 IP 位址，以符合指定的情況）
  2.    更新的指令碼，以符合指令碼可帶 （訂閱、 服務名稱等） 的環境中的使用者變數
  3.    在 PowerShell 中執行指令碼

**附註**︰ 中的 PowerShell 指令碼表示的區域，必須符合表示網路設定的 xml 檔案中的區域。

順利執行指令碼後，可能需要採取下列後的指令碼步驟︰

1.  設定防火牆規則，這中包含以下的章節標題︰ 防火牆規則描述。
2.  您也可以在 [參考資料] 區段會設定網頁伺服器和應用程式伺服器，若要允許使用此 DMZ 設定測試的簡單的 web 應用程式的兩個指令碼。

一旦指令碼順利執行規則會需要完成的防火牆，這一節中涵蓋︰ 防火牆規則。

## <a name="user-defined-routing-udr"></a>使用者定義路由 (UDR)
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

VNETLocal 永遠是針對 （ie 它會從變更 VNet VNet 根據每個特定 VNet 已定義之） 的特定網路 VNet 的已定義之地址 prefix(es)。 剩餘系統路由靜態，而且預設上方。

優先順序，先路由處理透過最長加上字首須相符 (LPM) 的方法，因此在資料表中的最特定路由會套用到指定的目標位址]。

因此，用於 [區域網路 (10.0.0.0/16) 的流量 （例如到 DNS01 server 10.0.2.4) 會被跨 10.0.0.0/16 路由因為其目的地 VNet。 換句話說，10.0.2.4，10.0.0.0/16 路由是最特定路由，即使 10.0.0.0/8/8 以及 0.0.0.0/0 也都可能會套用，但因為它們是小於特定不會影響此流量。 因此 10.0.2.4 的流量會有本機 VNet 躍，而且只將路由傳送至目的地。

如果供流量 10.1.1.1，例如，10.0.0.0/16 傳送不會套用，但 10.0.0.0/8 最特定而這流量會遭到捨棄 （「 黑色書背 」） 之後的下一個躍點為 Null。 

如果目的地未套用至任何 Null 前置詞或 VNETLocal 前置詞，則會遵循至少有特定路由傳送 0.0.0.0/0，並為一個躍點，查看 Azure 的網際網路邊緣路由至網際網路。

如果路由表格中有兩個相同的前置字元，以下是根據路由 「 來源 」 屬性的喜好設定的順序︰

1.  「 VirtualAppliance 「 = 使用者定義路由手動新增至資料表
2.  「 VPNGateway 「 = 動態路由 (BGP 搭配混合式網路時)，新增動態網路通訊協定，這些路由可能會變更一段時間的動態通訊協定會自動反映 peered 網路中的變更
3.  「 預設 」 = 系統路由與本機 VNet 靜態的項目，以上路由表格中所示。

>[AZURE.NOTE] 您現在可以使用 ExpressRoute 與 VPN 閘道器的使用者定義路由 (UDR) 若要強制輸出及輸入跨內部部署傳輸路由傳送至 [網路虛擬應用裝置 (NVA)。

#### <a name="creating-the-local-routes"></a>建立區域路由

在此範例中，需要兩個路由的資料表，一個每個 Frontend 和後端的子網路。 每個資料表會載入靜態路由適用於指定子網路。 此範例中，針對每個資料表都有三個路由︰

1. 本機的子網路流量與沒有下一個躍點定義為允許區域子網路流量略過防火牆
2. 虛擬網路流量與下一個躍點定義為防火牆，這會覆寫預設的規則，允許本機 VNet 流量路由傳送直接
3. 所有剩餘流量 (0/0) 的下一個躍點定義為防火牆

一旦建立路由表格的它們繫結到他們的子網路。 Frontend 子網路路由資料表中，一次建立並繫結至子網路看起來應該像這樣︰

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


例如，下列命令用來建立路由表、 新增使用者定義路由，並將繫結路由表子網路 (記事; 下方開頭為貨幣符號的任何項目 (例如︰ $BESubnet) 是從這份文件的 [參照] 區段中的指令碼的使用者定義變數):

1.  第一次必須建立基本的路由資料表。 此程式碼片段顯示建立後端子網路的資料表。 在 [指令碼對應表格也會建立 Frontend 子網路。

        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"

2.  路由表格建立之後，就可以新增特定的使用者定義的路徑。 此顯示剪下的所有流量 (0.0.0.0 / 0) 會都經由虛擬 （$VMIP [0]，變數用來傳遞中指派指令碼較舊版本中建立虛擬應用裝置時的 IP 位址） 的應用裝置。 在 [指令碼 Frontend 表格也會建立對應的規則。

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

3. 上述路由項目會覆寫預設 0.0.0.0 / 「 0 」 路由，但仍現有的預設 10.0.0.0/16 規則會讓流量路由傳送至目的地直接，而非網路虛擬應用裝置 VNet 內。 若要正確這種行為追蹤規則必須新增。

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]

4. 此時會進行選擇。 使用上述兩個路由所有的流量會將路由傳送至評估，甚至流量單一子網路內的防火牆。 這可能被理想的但若要允許流量路由傳送至本機參與防火牆不第三個子網路內，您可以新增特定的規則。 此路由狀態的任何地址 destine 的區域子網路可以只將那里直接 (NextHopType = VNETLocal)。

        Get-AzureRouteTable $BERouteTableName | `
            Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal

5.  最後，使用 [路由] 資料表中建立並填入 [使用者定義路由資料表必須現在繫結到子網路。 在 [指令碼前端路由表格也結合 Frontend 子網路。 以下是後端子網路的繫結指令碼。

        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
           -SubnetName $BESubnet `
           -RouteTableName $BERouteTableName

## <a name="ip-forwarding"></a>IP 轉送
小幫手] 來 UDR，功能 IP 轉寄功能。 這是允許接收流量不是收件者為應用裝置和再轉寄最終目的地資料虛擬應用裝置上的設定。

作為範例，如果從 AppVM01 DNS01 伺服器，進行要求 UDR 想路由此防火牆。 使用 IP 轉寄啟用，DNS01 目的地 (10.0.2.4) 的流量會接受應用裝置 (10.0.0.4) 並再轉寄給最終目的地 (10.0.2.4)。 不含 IP 轉寄防火牆上啟用，流量不會接受應用裝置，即使路由表有一個躍點為防火牆。 

>[AZURE.IMPORTANT] 請務必記得要啟用及使用者定義路由 IP 轉寄功能。

設定 IP 轉接是單一命令，且可以在 VM 建立時間。 此範例中的流程的程式碼片段的指令碼結束並分組 UDR 命令︰

1.  撥打在此情況下，為您虛擬應用裝置防火牆 VM 執行個體，並啟用 IP 轉接 (記事; 紅色開頭為貨幣符號中的任何項目 (例如︰ $VMName[0]) 是從這份文件的 [參照] 區段中的指令碼的使用者定義變數。 以括弧括住，[0]，將零代表 Vm 範例指令碼，而不必修改陣列中的第一個 VM，第一個 VM (VM 0) 必須是防火牆:）

        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
           Set-AzureIPForwarding -Enable

## <a name="network-security-groups-nsg"></a>網路安全性群組 (NSG)
在此範例中，會 NSG 群組內建，然後再載入以單一的規則。 此群組是繫結至主選單和後端子網路 (不 SecNet)。 宣告方式建立下列規則︰

1.  拒絕任何流量 （所有連接埠） 從網際網路整個 VNet （所有子網路）

雖然在此範例使用 NSGs，它的主要目的是為防護，手動設定錯誤的第二層。 我們想要封鎖所有傳入的流量從網際網路 [Frontend 或後端子網路流量應該僅限通過防火牆 SecNet 子網路，並執行 Frontend 或後端視如果子網路。 此外，UDR 規則的位置，任何未納入 Frontend 或後端子網路的流量會導向查看 （獎狀 UDR) 防火牆。 防火牆會看到為非對稱式的流程拖放想要的外寄流量。 因此有三個層級的安全性保護 Frontend 和後端子網路;1） 開啟端點 FrontEnd001 和 BackEnd001 雲端服務，2) NSGs 從網際網路]，3） 的防火牆卸除非對稱式流量拒絕的流量。

關於在此範例中的 [網路] 安全性群組一個有趣的地方是其包含的只有一個規則，所示，已拒絕整個虛擬網路想包括安全性子網路的網際網路流量。 

    Get-AzureNetworkSecurityGroup -Name $NSGName | `
        Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
        from the Internet" `
        -Type Inbound -Priority 100 -Action Deny `
        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
        -DestinationAddressPrefix VIRTUAL_NETWORK `
        -DestinationPortRange '*' `
        -Protocol *

不過，由於 NSG 只繫結到 Frontend 和後端的子網路時，規則不會處理流量到安全性的子網路連入。 如此一來，即使 NSG 規則會顯示任何地址沒有網際網路流量導向上 VNet，因為 NSG 未繫結至安全性子網路，流量流動安全性子網路。

    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $FESubnet -VirtualNetworkName $VNetName
    
    Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
        -SubnetName $BESubnet -VirtualNetworkName $VNetName

## <a name="firewall-rules"></a>防火牆規則
在防火牆後，轉寄規則會需要建立。 需要多防火牆規則，由於防火牆封鎖或轉接所有輸入、 輸出及內部 VNet 流量。 此外，所有輸入的流量會處理的防火牆叫用安全性服務公用 IP 位址 （在不同的連接埠）、。 最佳作法是圖表邏輯之前設定子網路流量而防火牆規則，以避免稍後重新。 下圖是防火牆規則，此範例中的邏輯檢視︰
 
![邏輯檢視的防火牆規則][2]

>[AZURE.NOTE] 根據所用的虛擬應用裝置網路，管理連接埠會視情況而定。 在此範例中 Barracuda NextGen 防火牆參照的使用連接埠 22、 801 和 807。 請參閱尋找確切的連接埠用來管理所用的裝置的應用裝置廠商文件。

### <a name="logical-rule-description"></a>邏輯規則描述
邏輯圖中，因為防火牆是唯一的資源在該子網路，且該圖表會顯示防火牆規則和如何他們邏輯允許或拒絕流量和非實際的路由的路徑，不會顯示安全性子網路。 此外，選取 RDP 流量較高的外部連接埠遠距連接埠 (8014 – 8026)，然後選取要比較對齊更容易可讀性的本機 IP 位址的最後兩個八位元組 （例如本機伺服器位址 10.0.1.4 是與外部連接埠 8014 相關聯），但是可以用任何較高的非衝突連接埠。

例如，我們需要規則的 7 種，這些規則類型如下所述︰

- （適用於輸入流量） 的外部規則︰
  1.    防火牆管理規則︰ 此應用程式重新導向規則允許流量傳遞至網路虛擬應用裝置管理連接埠。
  2.    RDP 規則 （每個 windows 伺服器）: （每個伺服器一個） 四個規則會允許透過 RDP 個別伺服器的管理。 這也可能會建立一個規則，根據網路虛擬應用裝置所用的功能將。
  3.    應用程式的資料傳輸規則︰ 有兩個應用程式傳輸規則，前端網頁流量，第一個和第二個後端流量 （例如網頁伺服器資料層）。 設定這些規則會取決於網路架構 （您的伺服器放置的位置），並流量流向 （流量，與哪些連接埠用的方向）。
      - 第一個規則，可讓達到應用程式伺服器的實際的應用程式流量。 雖然其他規則，允許的安全性、 管理等，應用程式的規則以及允許外部使用者或服務存取應用程式。 此範例中，針對單一網頁伺服器連接埠 80，因此單一防火牆應用程式規則將流量重新導向連入至外部 IP 的網頁伺服器內部 IP 位址。 重新導向的流量工作階段就會 NAT 已內部伺服器。
      - 第二個應用程式傳輸規則是後端規則，允許透過任何連接埠的網頁伺服器對談 AppVM01 伺服器 （但不是 AppVM02）。
- 內部規則 （適用於內部 VNet 流量）
  4.    輸出至網際網路規則︰ 此規則可讓任何網路傳遞到所選的網路流量。 此規則通常是預設規則已在防火牆後，但已停用狀態。 這個範例中，才可以啟用此規則。
  5.    DNS 規則︰ 此規則可讓只有 DNS （連接埠 53） 傳遞給 DNS 伺服器的流量。 此環境中，從 Frontend 後端的大部分流量遭到封鎖，這個規則特別允許 DNS 從任何區域的子網路。
  6.    若要子網路的子網路規則︰ 此規則可允許任何伺服器端子網路連線到任何伺服器上的前端子網路 （但不是相反）。
- 保全 （用於不符合上述任一的流量） 規則︰
  7.    拒絕所有的資料傳輸規則︰ 此一律採用 （而言優先順序），最後一個規則，因此如果流量流向無法符合任何將此規則卸除上述規則。 這是預設的規則，通常會啟動，沒有通常需修改。

>[AZURE.TIP] 在第二個應用程式的資料傳輸規則，任何連接埠允許此範例中，在真實案例的簡單的最特定的連接埠，位址範圍來縮小攻擊此規則。

<br />

>[AZURE.IMPORTANT] 所有上述規則建立之後，請務必要檢閱每一個規則，以確保會允許或拒絕視流量的優先順序。 例如，規則的順序優先順序。 您可以輕鬆進行鎖定的防火牆，因為正確排序的規則。 在 [最小值，確保防火牆本身的管理永遠絕對的最高優先順序規則。

### <a name="rule-prerequisites"></a>規則的先決條件
虛擬機器執行防火牆一個必要條件是公開端點。 處理流量防火牆適當的公用端點必須為開啟狀態。 有三種類型的流量在此範例中;1） 管理流量控制防火牆與防火牆規則，2） 來控制 windows 伺服器和 3） 應用程式流量 RDP 流量。 這些上方的防火牆規則邏輯檢視的一半，是流量類型的上方的三個資料行。

>[AZURE.IMPORTANT] 請記住，**所有**的流量會經過防火牆可以下重要 takeway。 因此 IIS01 伺服器的遠端桌面，即使是前結束雲端服務中和前端子網路上存取此伺服器我們會需要 RDP 連接埠 8014，防火牆，然後允許將 RDP 要求路由至 IIS01 RDP 連接埠的內部防火牆。 Azure 入口網站的 [連線] 按鈕無法使用，因為沒有直接 RDP 路徑至 IIS01 （就可以看到入口網站）。 這表示安全性服務和連接埠，例如 secscv001.cloudapp.net:xxxx （參照外部連接埠、 內部 IP 和連接埠的對應的上方圖表） 會從網際網路的所有連線。

端點可以開啟在 VM 建立時，或已完成的範例指令碼並下方顯示在此程式碼片段張貼建立 (請注意，為貨幣符號的任何項目開頭 (例如︰ $VMName[$i]) 是從這份文件的 [參照] 區段中的指令碼的使用者定義變數。 「 $I 」 以括弧括住，[$i]，表示陣列的 Vm 陣列中的特定 VM):

    Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
        -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
        Update-AzureVM

雖然不清楚以下所示的變數，但結束點的使用狀況**只**開啟安全性雲端服務。 這是為了確保所有輸入的流量處理 （路由，NAT，卸除） 防火牆。

需要管理用戶端安裝在電腦上管理防火牆，並建立所需的設定。 如何管理裝置，請參閱從您的防火牆 （或其他 NVA） 的文件廠商。 此區段與 [下一步] 區段，防火牆規則建立、 的其餘部分將說明透過廠商管理用戶端 （也就是不是 Azure 入口網站或 PowerShell） 本身的防火牆設定。

可以在這裡找到的用戶端下載並連線到此範例中使用 Barracuda 指示︰ [Barracuda NG 管理員](https://techlib.barracuda.com/NG61/NGAdmin)

一旦登入到防火牆，但之前建立防火牆規則，有兩個必要的物件類別可讓您建立的規則。網路與服務的物件。

例如，三個命名的網路物件應該已定義之 （一 Frontend 子網路和後端子網路，也網路物件的 IP 位址的 DNS 伺服器）。 若要建立具名的網路。開始從 Barracuda NG 管理員用戶端儀表板，瀏覽至 [設定] 索引標籤、 [操作組態] 區段中按一下 [規則集、 然後防火牆物件] 功能表底下按一下 [網路] 然後按一下 [新增]，請在 [編輯網路] 功能表中。 網路可以現在建立物件，藉由新增名稱] 和 [前置字元︰

![建立主選單網路物件][3]
 
這會建立命名的 FrontEnd 子網路的網路，應為後端子網路建立類似的物件。 現在您可以更輕鬆地防火牆規則中的名稱來參考子網路。

DNS 伺服器物件︰

![建立 DNS 伺服器物件][4]

將文件稍後 DNS 規則中利用此單一的 IP 位址參照。

第二個必要的物件是服務的物件。 這些可代表每個伺服器 RDP 連線連接埠。 現有 RDP 服務物件繫結到預設 RDP 連接埠，因為 3389，新服務可以建立為允許來自外部連接埠 (8014 8026) 的流量。 新的連接埠也可能會新增至現有的 RDP 服務，但可以建立便於示範，每個伺服器個別規則。 若要建立新的 RDP 規則伺服器。開始從 Barracuda NG 管理員用戶端儀表板，瀏覽至 [設定] 索引標籤在操作設定] 區段中，按一下 [規則集，然後 」 服務 」 防火牆物件] 功能表下、 向下捲動的服務清單，然後按一下選取的 「 RDP 」 服務。 以滑鼠右鍵按一下並選取 [複製]，然後以滑鼠右鍵按一下，選取 [貼。 現在可以編輯 RDP Copy1 服務物件。 以滑鼠右鍵按一下 RDP Copy1，然後選取 [編輯]，[編輯服務物件視窗就會出現最如下所示︰

![預設 RDP 規則的複本][5]

值然後可以代表 RDP 服務特定伺服器進行編輯。 AppVM01 上述的預設 RDP 規則應修改以反映新服務名稱、 描述及圖 8 圖表中使用外部 RDP 連接埠 (請注意︰ 連接埠 3389 RDP 預設值變更為此特定伺服器所用的外部連接埠、 若是 AppVM01 外部連接埠是 8025) 如下所示的已修改的服務:

![AppVM01 規則][6]
 
若要建立其他的伺服器; RDP 服務必須重複此程序AppVM02 DNS01，與 IIS01。 建立這些服務，就能規則建立簡單、 更明顯下一節。

>[AZURE.NOTE] 防火牆 RDP 服務，不需要兩個原因。第 1） 的防火牆 VM 是根據 Linux 圖像 SSH 想連接埠 22 用於 VM 管理，而非 RDP 和 2） 連接埠 22，且兩個其他管理連接埠所允許的第一個管理規則以利管理連線如下所述。

### <a name="firewall-rules-creation"></a>防火牆規則建立
有三種類型的 [在此範例中所使用的防火牆規則，它們都有不同的圖示︰

重新導向應用程式的規則︰![應用程式 [重新導向] 圖示][7]

目的地 NAT 規則︰![目的地 NAT 圖示][8]

階段規則︰![傳遞圖示][9]

可以 Barracuda 網站上找到這些規則的詳細資訊。

若要建立下列規則 （或驗證現有的預設規則），開始從 Barracuda NG 管理員用戶端儀表板中，瀏覽至 [設定] 索引標籤，在操作的設定] 區段按一下 [規則集。 稱為格線中，「 主要規則 」 會顯示現有的作用中] 與 [停用規則在這個防火牆上。 此格線右上角的小型、 綠色 [+] 按鈕，按一下此選項可建立新規則 (請注意︰ 您的防火牆可能會遭 「 鎖定 」 的變更，如果您看到 [標示為 「 鎖定 」] 按鈕，您無法建立或編輯規則，請按一下這個按鈕，即可 「 解除鎖定 」 的規則集，並允許編輯)。 如果您想要編輯現有的規則，選取該規則，以滑鼠右鍵按一下和 [編輯規則。

一旦您規則建立及/或修改必須放入防火牆，再啟動、 如果不是規則變更不會生效。 推入和啟動程序的詳細資料的規則描述下方說明。

完成此範例中所需的每個規則的詳細說明，如下所示︰

- **防火牆管理規則**︰ 重新導向此應用程式的規則允許將 Barracuda NextGen 防火牆傳遞到的網路虛擬應用裝置，在此範例中的管理連接埠的流量。 管理連接埠是 801、 807 和 22 （選擇性）。 內部及外部連接埠是相同的 （亦即沒有連接埠轉譯）。 此規則，設定管理存取是預設的規則與 （在 Barracuda NextGen 防火牆版本 6.1） 中的預設為啟用。

    ![防火牆管理規則][10]

>[AZURE.TIP] 此規則中的來源位址空間為 「 任何 」，如果已知管理 IP 位址範圍，減少此範圍會也會減少攻擊的管理連接埠。

- **RDP 規則**︰ 這些目的地 NAT 規則會允許透過 RDP 個別伺服器的管理。
有四個建立此規則時所需的要徑欄位︰
  1.    在 [來源] 欄位中使用來源 – 允許 RDP 從任何地方，參照 「 任何 」。
  2.    服務 – 使用適當的服務物件建立更早版本，在此例 」 AppVM01 RDP 」，外部連接埠重新導向至伺服器的本機 IP 位址和連接埠 3386 （預設 RDP 連接埠）。 此特定規則適用於 AppVM01 RDP 存取。
  3.    目的地 – 應該在*本機防火牆連接埠*、 「 DCHP 1 本機 IP 」 或 eth0 如果使用的靜態 Ip。 可能不同，如果您的網路應用裝置有多個本機介面序號 （eth0、 eth1 等）。 這是防火牆，會從送出的連接埠 （可能是接收的連接埠相同），實際路由的目的地是在 [目標清單的欄位。
  4.    重新導向-本節會告訴虛擬應用裝置最後將此流量重新導向的位置。 最簡單的重新導向是目標清單欄位 （選擇性） 置於 IP 和連接埠。 如果沒有連接埠用輸入要求的目的地連接埠會使用連接埠指定連接埠 （ie 沒有轉譯），也會 NAT 想以及 IP 位址。

    ![防火牆 RDP 規則][11]

    將需要建立四個 RDP 規則總計︰ 

  	|   規則名稱    | 伺服器  |   服務   |  目標清單  |
  	|----------------|---------|-------------|---------------|
  	| RDP-IIS01   | IIS01   | IIS01 RDP   | 10.0.1.4:3389 |
  	| RDP-DNS01   | DNS01   | DNS01 RDP   | 10.0.2.4:3389 |
  	| RDP-AppVM01 | AppVM01 | AppVM01 RDP | 10.0.2.5:3389 |
  	| RDP-AppVM02 | AppVM02 | AppVm02 RDP | 10.0.2.6:3389 |
  
>[AZURE.TIP] 向下的 [來源] 和 [服務欄位範圍縮小會減少攻擊的風險。 應該使用，讓功能的最限制的範圍。

- **應用程式的資料傳輸規則**︰ 有兩個應用程式傳輸規則，前端網頁流量，第一個和第二個後端流量 （例如網頁伺服器資料層）。 這些規則會取決於網路架構 （您的伺服器放置的位置），而且流量流向 （流量，與哪些連接埠用的方向）。

    第一次討論是前端傳輸規則的網站︰

    ![防火牆網頁規則][12]
 
    這項目的 NAT 規則可讓達到應用程式伺服器的實際的應用程式流量。 雖然其他規則，允許的安全性、 管理等，應用程式的規則以及允許外部使用者或服務存取應用程式。 此範例中，針對單一網頁伺服器連接埠 80，因此單一防火牆應用程式規則將流量重新導向連入至外部 IP 的網頁伺服器內部 IP 位址。

    **附註**︰ 指定在目標清單欄位中沒有連接埠，因此輸入的連接埠 80 （或 443 所選取的服務） 中的網頁伺服器重新導向時將會使用。 如果不同的連接埠接聽網頁伺服器，例如連接埠 8080，無法更新的目標清單的欄位，以允許的連接埠重新導向 10.0.1.4:8080。

    下一個應用程式傳輸規則為後端規則，允許透過任何服務的網頁伺服器對談 AppVM01 伺服器 （但不是 AppVM02）︰

    ![防火牆 AppVM01 規則][13]

    此階段規則允許達到 AppVM01 Frontend 子網路上的任何 IIS 伺服器 （IP 位址 10.0.2.5） 上任何連接埠，使用 [在 web 應用程式需要存取資料的任何通訊協定。

    在此螢幕擷取畫面 」\<明確目的地\>」 表示作為目的地 10.0.2.5 目的欄位中使用。 這可能是 [明確如下圖所示的具名網路物件 （如已完成的必要條件 DNS 伺服器）。 這是最防火牆將為其使用方法的系統管理員。 若要新增 10.0.2.5 為 Explict Desitnation，按兩下第一個空白列下\<明確目的地\>，並在出現的視窗中輸入地址。

    使用此傳遞的規則，因為這是內部流量，因此連線方法可以設定為 「 無 SNAT 」 需要沒有 NAT。

    **注意**︰ 此規則中的來源網路是任何資源 FrontEnd 子網路，如果只會有已知的特定數字的網頁伺服器，或其中一個網路物件資源可以建立更詳細的確切的 IP 位址，而非整個 Frontend 子網路。

>[AZURE.TIP] 此規則使用服務 「 任何 」，讓您更容易設定和使用範例應用程式，這也可讓 ICMPv4 (ping) 一個規則。 不過，這不是建議的做法。 連接埠與通訊協定 （「 服務 」） 應縮小可能可讓應用程式作業，以減少攻擊跨界限最小值。

<br />

>[AZURE.TIP] 雖然這項規則會顯示正在使用的明確目的地參照，應使用一致的方式整個防火牆設定。 建議您在使用命名的網路物件，以更容易的可讀性和性。 明確目的地只會用於在這裡顯示替代參考方法和通常不建議 （特別的複雜設定）。

- **輸出網際網路規則**︰ 將此規則可讓從傳遞給所選的目的地網路任何來源網路流量。 此規則預設的規則通常已經 Barracuda NextGen 防火牆，但已停用狀態。 以滑鼠右鍵按一下此規則可以存取啟動規則] 命令。 已新增為 [必要] 區段的 [這份文件中的參照的兩個本機子網路所建立的此規則的來源屬性修改此處所示的規則。

    ![防火牆輸出規則][14]

- **DNS 規則**︰ 將此規則可讓只有 DNS （連接埠 53） 傳遞給 DNS 伺服器的流量。 此環境中，從 FrontEnd 後端的大部分流量遭到封鎖，這個規則特別允許 DNS。

    ![防火牆 DNS 規則][15]

    **注意**︰ 此畫面擷取連線方法是包含在內。 此規則適用於內部的 IP 位址流量的內部 IP，因為沒有 NATing 必要時，此連線方法設為 「 無 SNAT 」 階段規則。

- **若要子網路的子網路規則**︰ 此傳遞規則就是已經啟動，並修改以允許任何伺服器端子網路連線到前端子網路上的任何伺服器上的預設規則。 此規則是連線方法可以設定為 [無 SNAT 所有內部流量。

    ![防火牆內部 VNet 規則][16]

    **附註**︰ 雙向] 核取方塊並未核取 （也就可以在大部分的規則] 核取），因為這可讓此規則 「 單向 」，這是嚴重規則、 從後端子網路前端網路，但不初始化連線。 如果已核取該核取方塊，此規則可讓雙向流量，從我們的邏輯圖表不希望。

- **拒絕所有的資料傳輸規則**︰ 此一律採用 （而言優先順序），最後一個規則，因此如果流量流向失敗，以符合下列任一前一個規則，將此規則卸除。 這是預設的規則，通常會啟動，沒有通常需修改。 

    ![防火牆拒絕規則][17]

>[AZURE.IMPORTANT] 所有上述規則建立之後，請務必要檢閱每一個規則，以確保會允許或拒絕視流量的優先順序。 例如，他們應該會顯示在主要格線中的轉接 Barracuda 管理用戶端規則的順序的規則。

## <a name="rule-activation"></a>規則啟動
使用邏輯圖表的規格修改規則集，必須上傳至防火牆，然後啟動規則集。

![防火牆規則啟動][18]
 
在管理用戶端的右上角會叢集的按鈕。 按一下 「 傳送變更 」] 按鈕，若要修改的規則傳送防火牆，然後按一下 「 啟動 」] 按鈕。
 
此範例環境建立的防火牆規則集啟動就完成。

## <a name="traffic-scenarios"></a>流量分析藍本
>[AZURE.IMPORTANT] 請記住，**所有**的流量會經過防火牆可索引鍵的 takeway。 因此 IIS01 伺服器的遠端桌面，即使是前結束雲端服務中和前端子網路上存取此伺服器我們會需要 RDP 連接埠 8014，防火牆，然後允許將 RDP 要求路由至 IIS01 RDP 連接埠的內部防火牆。 Azure 入口網站的 [連線] 按鈕無法使用，因為沒有直接 RDP 路徑至 IIS01 （就可以看到入口網站）。 這表示安全性服務和連接埠，例如 secscv001.cloudapp.net:xxxx 會從網際網路的所有連線。

下列情況下，下列防火牆規則應該位於位置︰

1.  防火牆管理
2.  若要 IIS01 RDP
3.  若要 DNS01 RDP
4.  若要 AppVM01 RDP
5.  若要 AppVM02 RDP
6.  Web 應用程式流量
7.  應用程式 AppVM01 流量
8.  連到網際網路
9.  若要 DNS01 Frontend
10. 內部子網路流量 （僅限前端後端）
11. 全部拒絕

實際的防火牆規則集很可能會有這些除了許多其他規則，在任何指定的防火牆規則也會有不同的優先順序數字，比列於此處的項目。 此清單和相關聯的數字是提供只這些十的規則與在它們之間的相對優先順序之間的相關性。 換句話說;實際的防火牆，「 RDP 至 IIS01 」 可能是規則的數字 5，但只要是 「 防火牆管理 」 規則，而且上方的 「 RDP 至 DNS01 」 規則它會對齊皆這份清單。 清單也有助於下方案例允許贅述。例如 」 轉址規則 9 (DNS)]。 同時也贅述，四個 RDP 規則將會共同稱為 「 RDP 規則 」 時的流量分析藍本無關 RDP。

也回收網路安全性群組是就地 Frontend 和後端的子網路上輸入的網際網路流量。

#### <a name="allowed-internet-to-web-server"></a>（允許）網際網路網頁伺服器
1.  網際網路使用者要求 HTTP 頁面從 SecSvc001.CloudApp.Net （網際網路對雲端服務）
2.  雲端服務傳送流量，透過開啟的端點上 10.0.0.4:80 的連接埠 80 防火牆介面
3.  指派給安全性子網路沒有 NSG，因此系統 NSG 規則允許防火牆流量
4.  流量碰到內部的 IP 位址的防火牆 (10.0.1.4)
5.  防火牆開始處理規則︰
  1.    FW 規則 1 （轉址管理） 不會套用，移至下一個規則
  2.    FW 規則 2-5 （RDP 規則） 不會套用，移至下一個規則
  3.    FW 規則 6 (應用程式︰ 網頁) 會套用，允許流量，防火牆 Nat 它 10.0.1.4 (IIS01)
6.  Frontend 子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用 (此流量已 NAT 想防火牆，因此來源地址時立即防火牆的安全性子網路和 Frontend 子網路 NSG 看到為 「 本機 」 流量，因此允許)，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
7.  IIS01 網路流量接聽收到這項要求，然後開始處理要求
8.  IIS01 嘗試啟動 AppVM01 後端子網路上的 FTP 工作階段
9.  Frontend 子網路上的 UDR 路由可讓您的防火牆躍
10. 在主選單子網路上的沒有輸出規則，允許流量
11. 防火牆開始處理規則︰
  1.    FW 規則 1 （轉址管理） 不會套用，移至下一個規則
  2.    FW 規則 2-5 （RDP 規則） 不會套用，移至下一個規則
  3.    FW 規則 6 (應用程式︰ 網頁) 不會套用，移至下一個規則
  4.    FW 規則 7 (應用程式︰ 後端) 會套用，允許流量、 防火牆轉寄 10.0.2.5 (AppVM01) 的流量
12. 後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
13.  AppVM01 收到要求，並啟動工作階段，並回應
14. 後端子網路上的 UDR 路由可讓您的防火牆躍
15. 由於允許回應的後端子網路上沒有輸出 NSG 規則
16. 因為這會傳回流量既有的工作階段防火牆會傳送至網頁伺服器 (IIS01) 的回應
17. Frontend 子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
18. IIS 伺服器收到回應、 完成 AppVM01，與交易，然後完成 [建置 HTTP 回應此 HTTP 回應傳送至要求者
19. 由於允許回應 Frontend 子網路上沒有輸出 NSG 規則
20. HTTP 回應結果的防火牆，因為這是既有的 NAT 工作階段的回應接受防火牆
21. 防火牆然後重新導向網際網路使用者的回應
22. 因為有不輸出 NSG 規則或 UDR 躍點允許回應，則 Frontend 子網路和網際網路使用者，會收到要求的網頁。

#### <a name="allowed-internet-rdp-to-backend"></a>（允許）網際網路 RDP 至後端
1.  在網際網路上的伺服器管理員要求透過 SecSvc001.CloudApp.Net:8025，8025 哪裡指派給使用者的連接埠號碼的 「 RDP 至 AppVM01 」 防火牆規則 AppVM01 RDP 工作階段
2.  雲端服務傳送流量透過開啟端點連接埠 8025 10.0.0.4:8025 上的防火牆介面
3.  指派給安全性子網路沒有 NSG，因此系統 NSG 規則允許防火牆流量
4.  防火牆開始處理規則︰
  1.    FW 規則 1 （轉址管理） 不會套用，移至下一個規則
  2.    FW 規則 2 (RDP IIS) 不會套用，移至下一個規則
  3.    FW 規則 3 (RDP DNS01) 不會套用，移至下一個規則
  4.    FW 規則 4 (RDP AppVM01) 會套用，，允許流量，防火牆 Nat 它 10.0.2.5:3386 （RDP 連接埠 AppVM01）
5.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用 (此流量的 NAT 想防火牆，因此來源地址時立即防火牆的安全性子網路和後端子網路 NSG 看到為 「 本機 」 流量，因此允許)，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
6.  AppVM01 RDP 流量接聽和回應
7.  沒有輸出 NSG 規則]，套用預設規則與允許寄流量
8.  UDR 為躍將外寄流量路由至防火牆
9.  防火牆因為這會傳回既有的工作階段的流量會回到網際網路使用者將回應
10. 已啟用 RDP 工作階段
11. AppVM01 會提示使用者名稱的密碼

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>（允許）DNS 伺服器上的網頁伺服器 DNS 查閱
1.  網頁伺服器、 IIS01、 需求資料摘要 www.data.gov，但若要解決地址的需求。
2.  網路設定 VNet 清單 DNS01 (10.0.2.4 後端子網路上) 為主要的 DNS 伺服器，IIS01 就會將 DNS 邀請傳送至 DNS01
3.  UDR 為躍將外寄流量路由至防火牆
4.  沒有輸出 NSG 規則繫結至 Frontend 子網路，允許流量
5.  防火牆開始處理規則︰
  1.    FW 規則 1 （轉址管理） 不會套用，移至下一個規則
  2.    FW 規則 2-5 （RDP 規則） 不會套用，移至下一個規則
  3.    套用轉址規則 6 與 7 （應用程式的規則） 沒有，移至下一個規則
  4.    FW 規則 8 （以網際網路） 不會套用，移至下一個規則
  5.    套用轉址規則 9 (DNS)、 允許流量防火牆轉寄 10.0.2.4 (DNS01) 的流量
6.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
7.  DNS 伺服器收到要求
8.  DNS 伺服器沒有快取的地址，並詢問在網際網路上的根 DNS 伺服器
9.  UDR 為躍將外寄流量路由至防火牆
10. 後端子網路上的沒有輸出 NSG 規則，允許流量
11. 防火牆開始處理規則︰
  1.    FW 規則 1 （轉址管理） 不會套用，移至下一個規則
  2.    FW 規則 2-5 （RDP 規則） 不會套用，移至下一個規則
  3.    套用轉址規則 6 與 7 （應用程式的規則） 沒有，移至下一個規則
  4.     套用轉址規則 8 （以網際網路）、 允許流量、 工作階段是在網際網路上的根 DNS 伺服器 SNAT 出
12. 網際網路 DNS 伺服器回應，由於這個工作階段從防火牆啟動的回應接受防火牆
13. 這是既有的工作階段，請為防火牆轉寄起始伺服器，DNS01 回應
14. 後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
15. DNS 伺服器接收快取的回應，然後回應回到 IIS01 初始要求
16. 後端子網路上的 UDR 路由可讓您的防火牆躍
17. 沒有輸出 NSG 規則存在於後端子網路，允許流量
18. 這是防火牆上的既有工作階段，回到 IIS 伺服器防火牆轉寄回應
19. Frontend 子網路開始輸入的規則處理︰
  1.    沒有 NSG 規則套用至輸入 Frontend 子網路，所以無 NSG 的規則套用至後端子網路流量
  2.    預設系統規則，允許子網路之間的流量會允許此流量，因此會允許流量
20. IIS01 從 DNS01 接收回應

#### <a name="allowed-backend-server-to-frontend-server"></a>（允許）Frontend 伺服器的後端伺服器
1.  登入透過 RDP AppVM02 系統管理員要求檔案直接從 IIS01 伺服器透過 windows 檔案總管
2.  後端子網路上的 UDR 路由可讓您的防火牆躍
3.  由於允許回應的後端子網路上沒有輸出 NSG 規則
4.  防火牆開始處理規則︰
  1.    FW 規則 1 （轉址管理） 不會套用，移至下一個規則
  2.    FW 規則 2-5 （RDP 規則） 不會套用，移至下一個規則
  3.    套用轉址規則 6 與 7 （應用程式的規則） 沒有，移至下一個規則
  4.    FW 規則 8 （以網際網路） 不會套用，移至下一個規則
  5.    FW 規則 9 (DNS) 不會套用，移至下一個規則
  6.    套用轉址規則 10 （內部子網路），並允許流量、 防火牆將流量傳遞給 10.0.1.4 (IIS01)
5.  Frontend 子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
6.  假設適當的驗證及授權，IIS01 接受要求並回應
7.  Frontend 子網路上的 UDR 路由可讓您的防火牆躍
8.  由於允許回應 Frontend 子網路上沒有輸出 NSG 規則
9.  這是防火牆上現有的工作階段允許此回應和防火牆會傳回 AppVM02 的回應
10. 後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 （組塊網際網路） 不會套用，移至下一個規則
  2.    預設 NSG 規則允許子網路子網路流量，允許流量、 停止 NSG 規則處理
11. AppVM02 收到回應

#### <a name="denied-internet-direct-to-web-server"></a>（拒絕）網際網路直接網頁伺服器
1.  網際網路使用者嘗試透過 FrontEnd001.CloudApp.Net 服務存取網頁伺服器，IIS01，
2.  沒有開啟 HTTP 流量的端點，因為這不會傳送到雲端服務，並可連到伺服器
3.  如果基於某種原因開啟端點，Frontend 子網路上的 NSG （組塊網際網路） 會被封鎖流量
4.  最後，Frontend 子網路 UDR 路由傳送所有外寄流量從 IIS01 到下一個躍點，為防火牆，防火牆會看到以下訊息為非對稱式流量拖輸出的回應，因此有至少三個獨立的保護層之間網際網路和 IIS01 透過防止未經授權/不當存取其雲端服務。

#### <a name="denied-internet-to-backend-server"></a>（拒絕）網際網路後端伺服器
1.  網際網路使用者嘗試透過 BackEnd001.CloudApp.Net 服務存取 AppVM01 上的檔案
2.  沒有開啟的檔案共用的端點，因為這不會傳送雲端服務，並可連到伺服器
3.  如果基於某種原因開啟端點，NSG （組塊網際網路） 會被封鎖流量
4.  最後，UDR 路由傳送所有外寄流量從 AppVM01 到下一個躍點，為防火牆，防火牆會看到以下訊息為非對稱式流量拖輸出的回應，因此有至少三個獨立的保護層之間網際網路和 AppVM01 透過防止未經授權/不當存取其雲端服務。

#### <a name="denied-frontend-server-to-backend-server"></a>（拒絕）後端伺服器 Frontend 伺服器
1.  假設 IIS01 受損，而且正在嘗試掃描後端子網路伺服器的惡意程式碼。
2.  Frontend 子網路 UDR 路由想任何外寄流量從傳送 IIS01 到防火牆為一個躍點。 這不是洩漏 VM 可以變更的項目。
3.  防火牆會處理傳輸，如果是 AppVM01，或可能無法 （因為轉址規則 7 和 9） 防火牆允許流量的 DNS 查閱的 DNS 伺服器的要求。 想要封鎖的轉址規則 11 （拒絕所有） 的所有其他流量。
4.  如果進階威脅偵測已啟用防火牆 （其不說明這份文件，請參閱進階威脅功能您指定的網路應用裝置的廠商文件），就會允許基本的轉寄規則這份文件中所討論的偶數流量可能無法如果流量包含已知的簽章或加上標幟的進階的威脅規則的圖樣。

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>（拒絕）在 [DNS 伺服器上的網際網路 DNS 查閱
1.  網際網路使用者嘗試查閱的內部 DNS 記錄上 DNS01 BackEnd001.CloudApp.Net 服務 
2.  沒有開啟 DNS 流量的端點，因為這不會傳送到雲端服務，並可連到伺服器
3.  如果基於某種原因開啟端點，NSG 規則 （組塊網際網路） Frontend 子網路上的會封鎖流量
4.  最後後, 端子網路 UDR 路由傳送所有外寄流量從 DNS01 到下一個躍點，為防火牆，防火牆會看到以下訊息為非對稱式流量拖輸出的回應，因此有至少三個獨立的保護層之間網際網路和 DNS01 透過防止未經授權/不當存取其雲端服務。

#### <a name="denied-internet-to-sql-access-through-firewall"></a>（拒絕）透過防火牆 SQL 存取網際網路
1.  網際網路使用者要求 SQL 資料從 SecSvc001.CloudApp.Net （網際網路對雲端服務）
2.  沒有開啟 SQL 的端點，因為這不會傳送雲端服務，並不會達到防火牆
3.  如果基於某種原因開啟 SQL 端點，防火牆會開始處理規則︰
  1.    FW 規則 1 （轉址管理） 不會套用，移至下一個規則
  2.    FW 規則 2-5 （RDP 規則） 不會套用，移至下一個規則
  3.    套用轉址規則 6 與 7 （應用程式的規則） 沒有，移至下一個規則
  4.    FW 規則 8 （以網際網路） 不會套用，移至下一個規則
  5.    FW 規則 9 (DNS) 不會套用，移至下一個規則
  6.    FW 規則 10 （內部子網路） 不會套用，移至下一個規則
  7.    套用轉址規則 11 （拒絕所有），流量封鎖、 停止規則處理


## <a name="references"></a>參照
### <a name="main-script-and-network-config"></a>主要指令碼和網路設定
PowerShell 指令碼檔儲存完整的指令碼。 將網路設定儲存成一個名為 「 NetworkConf2.xml 」 檔案。
如有需要請修改使用者定義的變數。 執行指令碼，然後依照防火牆規則設定指示以上。

#### <a name="full-script"></a>完整的指令碼
這個指令碼會根據使用者定義變數︰

1.  連線至 Azure 的訂閱
2.  建立新的儲存空間帳戶
3.  建立新的 VNet 及三個子網路設定檔中的定義
4.  建立五個虛擬機器;1 的防火牆和 4 的 windows server Vm
5.  設定 UDR 包括︰
  1.    建立兩個新的路由資料表
  2.    新增路由至資料表
  3.    將表格繫結到適當的子網路
6.  啟用上 NVA IP 轉送
7.  設定 NSG 包括︰
  1.    建立 NSG
  2.    新增規則
  3.    繫結至適當的子網路的 NSG

這個 PowerShell 指令碼應執行在本機電腦或伺服器連線到網際網路。

>[AZURE.IMPORTANT] 執行這個指令碼時，可能會有警告或彈入 PowerShell 其他資訊訊息。 僅限的錯誤訊息，以紅色都必須注意的問題。

    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwading from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA
    
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.
    
      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4
     
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4
     
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
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
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
    
    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}
    
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
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }
    
    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan
    
      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"
    
      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal
    
      # Assoicate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName
    
     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
    
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
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
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
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
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "雙向 DMZ NVA、 NSG，與 UDR"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "邏輯檢視的防火牆規則"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "建立主選單網路物件"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "建立 DNS 伺服器物件"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "預設 RDP 規則的複本"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "AppVM01 規則"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "應用程式 [重新導向] 圖示"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "目的地 NAT 圖示"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "傳送圖示"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "防火牆管理規則"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "防火牆 RDP 規則"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "防火牆網頁規則"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "防火牆 AppVM01 規則"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "防火牆輸出規則"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "防火牆 DNS 規則"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "防火牆內部 VNet 規則"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "防火牆拒絕規則"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "防火牆規則啟動"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
