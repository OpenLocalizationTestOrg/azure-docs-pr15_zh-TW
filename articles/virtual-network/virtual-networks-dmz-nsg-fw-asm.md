<properties
   pageTitle="DMZ 範例 – 建立 DMZ 保護防火牆與 NSGs 應用程式 |Microsoft Azure"
   description="建立 DMZ 防火牆與網路安全性群組 (NSG)"
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

# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>範例 2-建立 DMZ 保護防火牆與 NSGs 應用程式

[返回 [安全性邊界最佳作法頁面][HOME]

此範例會建立 DMZ 防火牆、 四個 windows 伺服器與網路安全性群組。 它也會引導執行每個相關的命令，以提供更深入瞭解每個步驟。 也有提供您採取進階的流量分析藍本區段逐步流量經由防衛 DMZ 的圖層的方式。 最後，在參照區段是完整的程式碼，來建立此環境中測試並嘗試使用各種情況的指示。 

![輸入的 DMZ NVA 與 NSG][1]

## <a name="environment-description"></a>環境描述
在此範例中有訂閱包含下列動作︰

- 兩個雲端服務: 「 FrontEnd001 」 和 「 BackEnd001 」
- 虛擬網路 「 CorpNetwork 」，具有兩個子網路: 「 FrontEnd 」 和 「 後端 」
- 單一網路安全性群組套用至這兩個子網路
- 網路的虛擬應用，在此範例中 Barracuda NextGen 防火牆來說，連線到 Frontend 子網路
- Windows 伺服器代表應用程式網頁伺服器 (「 IIS01 」)
- 代表回應用程式的兩個視窗伺服器端伺服器 （「 AppVM01 」、 「 AppVM02 」）
- Windows 伺服器代表 DNS 伺服器 (「 DNS01 」)

>[AZURE.NOTE] 雖然這個範例使用 Barracuda NextGen 防火牆，許多不同的網路虛擬就無法使用這個範例中。

在下方的 [參考資料] 區段中有會建立大部分上述的環境的 PowerShell 指令碼。 建置 Vm 和虛擬網路，完成的範例指令碼，雖然不說明這份文件中的詳細資料。
 
若要建立環境︰

  1.    儲存包含在 [參考資料] 區段中的網路設定的 xml 檔案 （更新名稱、 位置及 IP 位址，以符合指定的情況）
  2.    更新的指令碼，以符合指令碼可帶 （訂閱、 服務名稱等） 的環境中的使用者變數
  3.    在 PowerShell 中執行指令碼

**附註**︰ 中的 PowerShell 指令碼表示的區域，必須符合表示網路設定的 xml 檔案中的區域。

順利執行指令碼後，可能需要採取下列後的指令碼步驟︰

1.  設定防火牆規則，這中包含以下的章節標題︰ 防火牆規則。
2.  您也可以在 [參考資料] 區段會設定網頁伺服器和應用程式伺服器，若要允許使用此 DMZ 設定測試的簡單的 web 應用程式的兩個指令碼。

下一節說明最相對於網路安全性群組的指令碼陳述式。

## <a name="network-security-groups-nsg"></a>網路安全性群組 (NSG)
此範例中，NSG 群組是內建，然後再載入六個規則。 

>[AZURE.TIP] 一般而言，您應該先建立特定的 「 允許 」 規則首尾然後一般的 「 拒絕 」 規則。 指派的優先順序即表示規則會評估第一個。 一旦流量找到要套用至某特定規則時，會不評估任何進一步的規則。 NSG 規則可以套用中輸入或輸出方向 （從子網路的觀點）。

宣告方式，是輸入流量建置下列規則︰

1.  允許內部 DNS 流量 （連接埠 53）
2.  RDP （連接埠 3389） 從網際網路任何 vm 允許流量
3.  允許 HTTP 流量 （連接埠 80） 從網際網路 NVA （防火牆）
4.  允許任何流量 （所有連接埠） IIS01 AppVM1
5.  任何 （所有連接埠） 來自網際網路流量為整個拒絕 VNet （兩個子網路）
6.  拒絕任何流量 （所有連接埠） Frontend 子網路到後端的子網路

使用這些規則繫結至每一個子網路，如果 HTTP 要求輸入從網際網路網頁伺服器，而這兩種規則 3 （允許） 和 5 （拒絕） 會套用，但由於規則 3 具有較高的優先順序，只將套用的規則 5 就不會發生。 因此防火牆就會允許 HTTP 要求。 如果連線 DNS01 伺服器嘗試相同流量，規則 5 （拒絕） 就會套用至第一個，並不想要允許流量傳送至伺服器。 規則 6 （拒絕） 封鎖從交談 （除了允許流量規則 1 到 4） 的後端子網路 Frontend 子網路，這可保護的大小寫的後端網路駭客侵入網頁上的應用程式的主選單中，而攻擊會有限制存取後端 」 受保護 」 的網路 （僅限公開 AppVM01 伺服器上的資源）。

有預設的輸出規則，允許出的網際網路流量。 例如，我們是允許外寄流量，並不修改任何輸出規則。 若要鎖定中的傳輸路由使用者定義的說明，同時必要時，這會在不同的範例的可探索找到[主要的安全邊界文件]中[HOME]。

上述討論的 NSG 規則，兩者十分相似 NSG 規則[範例]1-建立簡單的 NSGs DMZ[Example1]。 請檢閱 NSG 描述中的每個 NSG 規則和其屬性的詳細檢視文件。

## <a name="firewall-rules"></a>防火牆規則
需要管理用戶端安裝在電腦上管理防火牆，並建立所需的設定。 如何管理裝置，請參閱從您的防火牆 （或其他 NVA） 的文件廠商。 本節的其餘部分將說明透過廠商管理用戶端 （也就是不是 Azure 入口網站或 PowerShell） 本身的防火牆設定。

可以在這裡找到的用戶端下載並連線到此範例中使用 Barracuda 指示︰ [Barracuda NG 管理員](https://techlib.barracuda.com/NG61/NGAdmin)

在防火牆後，轉寄規則會需要建立。 此範例中只路由網際網路流量中繫結至防火牆]，然後網頁伺服器，因為需要只有一個轉接 NAT 規則。 在此範例中使用 Barracuda NextGen 防火牆規則就是目的地 NAT 規則 (「 Dst NAT 」)，將此流量。

若要建立下列規則 （或驗證現有的預設規則），開始從 Barracuda NG 管理員用戶端儀表板中，瀏覽至 [設定] 索引標籤，在操作的設定] 區段按一下 [規則集。 稱為格線中，「 主要規則 」 會顯示現有的作用中] 與 [停用規則在防火牆。 在格線的右上角的小，綠色 [+]] 按鈕，按一下此選項可建立新規則 (請注意︰ 您的防火牆可能會遭 「 鎖定 」 的變更，如果您看到按鈕標示的 「 鎖定 」，而且您無法建立或編輯規則，請按一下這個按鈕，即可 「 解除鎖定 」 的規則集，並允許編輯)。 如果您想要編輯現有的規則，選取該規則，以滑鼠右鍵按一下和 [編輯規則。

建立新的規則，並提供的名稱，例如 「 WebTraffic 」。 

目的地 NAT 規則圖示看起來像這樣︰![目的地 NAT 圖示][2]

規則本身看起來可能像這樣︰

![防火牆規則][3]

以下任何連入叫用防火牆的地址嘗試連絡 HTTP （連接埠 80 或 443 的 HTTPS） 會送出的防火牆 」 DHCP1 本機 IP 」 介面並重新導向到網頁伺服器 10.0.1.5 的 IP 位址。 需要沒有連接埠變更，因為流量是傳入的連接埠 80 和連接埠 80 前往網頁伺服器。 不過，目標清單中可能有 10.0.1.5:8080 如果我們的網頁伺服器聆聽連接埠 8080 因此翻譯的連入連接埠 80 防火牆上輸入的連接埠 8080 網頁伺服器上。

連線方法應該也被如此，目的規則從網際網路]，是最適當的 「 動態 SNAT 」。 

雖然已經建立一個規則，但請務必其優先順序設定正確。 如果此新增規則位於 （下方的 「 BLOCKALL 」 規則） 下的格線的防火牆上的所有規則中它會永遠不會進入播放。 請確定新建的傳輸規則的 web 上方 BLOCKALL 規則。

一旦建立規則，必須放入防火牆，再啟動、 如果不是規則變更不會生效。 推入和啟動程序是下一節所述。

## <a name="rule-activation"></a>規則啟動
使用規則集，將此規則修改，必須上傳至防火牆，啟動規則集。

![防火牆規則啟動][4]

在管理用戶端的右上角會叢集的按鈕。 按一下 「 傳送變更 」] 按鈕，若要修改的規則傳送防火牆，然後按一下 「 啟動 」] 按鈕。

此範例環境建立的防火牆規則集啟動就完成。 您也可以執行 [參考資料] 區段中的文章建立指令碼加入此環境的應用程式，若要測試下方流量的情況。

>[AZURE.IMPORTANT] 請務必瞭解您不會直接按網頁伺服器。 當瀏覽器從 FrontEnd001.CloudApp.Net 要求 HTTP 頁面時，HTTP 端點 （連接埠 80） 會傳此流量到防火牆不是網頁伺服器。 防火牆然後根據規則建立網頁伺服器要求的 Nat 上方。

## <a name="traffic-scenarios"></a>流量分析藍本

#### <a name="allowed-web-to-web-server-through-firewall"></a>（允許）透過防火牆 web 至網頁伺服器
1.  網際網路使用者要求 HTTP 頁面從 FrontEnd001.CloudApp.Net （網際網路對雲端服務）
2.  雲端服務傳送流量，透過開啟的端點上 10.0.1.4:80 的連接埠 80 防火牆本機介面
3.  Frontend 子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    NSG 規則 3 （網際網路的防火牆） 會套用，流量允許、 停止規則處理
4.  流量碰到內部的 IP 位址的防火牆 (10.0.1.4)
5.  這是連接埠 80 流量，將它重新導向至網頁伺服器 IIS01，請參閱防火牆轉寄規則
6.  IIS01 網路流量接聽收到這項要求，然後開始處理要求
7.  IIS01 要求 SQL Server 上 AppVM01 資訊
8.  在主選單子網路上的沒有輸出規則，允許流量
9.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    NSG 規則 3 （網際網路的防火牆） 不會套用，移至下一個規則
  4.    NSG 規則 4 (IIS01 至 AppVM01) 並套用允許流量、 停止處理規則
10. AppVM01 接收 SQL 查詢並回應
11. 由於後端子網路上沒有輸出規則允許回應
12. Frontend 子網路開始輸入的規則處理︰
  1.    沒有 NSG 規則套用至輸入 Frontend 子網路，所以無 NSG 的規則套用至後端子網路流量
  2.    預設系統規則，允許子網路之間的流量會允許此流量，因此會允許流量。
13. IIS 伺服器收到 SQL 回應完成 HTTP 回應和會傳送要求給
14. 這是從防火牆的 NAT 工作階段，因為回應目的地 （一） 是防火牆
15. 防火牆從網頁伺服器接收回覆和轉寄回到網際網路使用者
16. 因為有允許沒有輸出規則 Frontend 子網路上的回應，則與網際網路會收到要求的網頁。

#### <a name="allowed-rdp-to-backend"></a>（允許）若要後端 RDP
1.  在網際網路上的伺服器管理員要求上 BackEnd001.CloudApp.Net:xxxxx xxxxx 哪裡 RDP 至 AppVM01 （Azure 入口網站上，或透過 PowerShell，可找到指派的連接埠） 的隨機指定連接埠號碼 AppVM01 RDP 工作階段
2.  由於防火牆只聆聽 FrontEnd001.CloudApp.Net 地址，不涉及與此流量
3.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 會套用，流量允許、 停止規則處理
4.  沒有輸出規則]，套用預設規則與允許寄流量
5.  已啟用 RDP 工作階段
6.  AppVM01 會提示使用者名稱的密碼

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>（允許）DNS 伺服器上的網頁伺服器 DNS 查閱
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>（允許）在 AppVM01 上的網頁伺服器 access 檔案
1.  IIS01 要求 AppVM01 上的檔案
2.  在主選單子網路上的沒有輸出規則，允許流量
3.  後端子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    NSG 規則 3 （網際網路的防火牆） 不會套用，移至下一個規則
  4.    NSG 規則 4 (IIS01 至 AppVM01) 並套用允許流量、 停止處理規則
4.  AppVM01 收到要求與回應檔案 （假設有權存取）
5.  由於後端子網路上沒有輸出規則允許回應
6.  Frontend 子網路開始輸入的規則處理︰
  1.    沒有 NSG 規則套用至輸入 Frontend 子網路，所以無 NSG 的規則套用至後端子網路流量
  2.    預設系統規則，允許子網路之間的流量會允許此流量，因此會允許流量。
7.  IIS 伺服器接收檔案

#### <a name="denied-web-direct-to-web-server"></a>（拒絕）直接網頁伺服器網頁
由於網頁伺服器、 IIS01，以及防火牆位於相同的雲端服務使用者共用相同的公用具 IP 位址。 因此任何 HTTP 的流量會導向至防火牆。 要求會提供成功，就無法直接移至網頁伺服器，它，以傳遞的設計，透過防火牆第一次。 請參閱交通流量的此區段中第一種情況。

#### <a name="denied-web-to-backend-server"></a>（拒絕）後端伺服器網頁
1.  網際網路使用者嘗試透過 BackEnd001.CloudApp.Net 服務存取 AppVM01 上的檔案
2.  沒有開啟的檔案共用的端點，因為這不會傳送雲端服務，並可連到伺服器
3.  如果基於某種原因開啟端點，NSG 規則 5 （網際網路 VNet） 封鎖流量

#### <a name="denied-web-dns-lookup-on-dns-server"></a>（拒絕）在 [DNS 伺服器上的 web DNS 查閱
1.  網際網路使用者嘗試查閱的內部 DNS 記錄上 DNS01 BackEnd001.CloudApp.Net 服務
2.  沒有 dns 開啟的端點，因為這不會傳送雲端服務，並可連到伺服器
3.  如果基於某種原因開啟端點，NSG 規則 5 （網際網路 VNet） 封鎖流量 (請注意︰ 該規則 1 (DNS) 不會套用有兩個原因，第一次來源地址是網際網路、 這個規則只套用到本機的 VNet 做為來源，也這是允許規則，讓它會永遠不會拒絕流量)

#### <a name="denied-web-to-sql-access-through-firewall"></a>（拒絕）透過防火牆 SQL 存取的網站
1.  網際網路使用者要求 SQL 資料從 FrontEnd001.CloudApp.Net （網際網路對雲端服務）
2.  沒有開啟 SQL 的端點，因為這不會傳送雲端服務，並不會達到防火牆
3.  如果基於某種原因開啟端點，Frontend 子網路開始輸入的規則處理︰
  1.    NSG 規則 1 (DNS) 不會套用，移至下一個規則
  2.    NSG 規則 2 (RDP) 不會套用，移至下一個規則
  3.    NSG 規則 2 （網際網路的防火牆） 會套用，流量允許、 停止規則處理
4.  流量碰到內部的 IP 位址的防火牆 (10.0.1.4)
5.  防火牆 sql 具有沒有轉寄規則和卸除流量

## <a name="conclusion"></a>結束時
這是相當直接轉寄的方式保護您的應用程式的防火牆和隔離從輸入流量的後端子網路。

更多範例，以及網路安全性界限的概觀，請參閱[以下][HOME]。

## <a name="references"></a>參照
### <a name="main-script-and-network-config"></a>主要指令碼和網路設定
PowerShell 指令碼檔儲存完整的指令碼。 將網路設定儲存成一個名為 「 NetworkConf2.xml 」 檔案。
如有需要請修改使用者定義的變數。 執行指令碼，然後依照防火牆規則設定指示以上。

#### <a name="full-script"></a>完整的指令碼
這個指令碼會根據使用者定義變數︰

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
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
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
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "輸入與 NSG DMZ"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "目的地 NAT 圖示"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "防火牆規則"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "防火牆規則啟動"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
