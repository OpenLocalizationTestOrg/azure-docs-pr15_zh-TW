<properties 
    pageTitle="Azure 虛擬網路整合應用程式" 
    description="顯示如何連線到新的或現有 Azure 虛擬網路 Azure 應用程式服務中的應用程式" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure 虛擬網路整合應用程式 #

這份文件說明 Azure 應用程式服務虛擬網路的整合功能，並說明如何設定在[Azure 應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714)應用程式。  如果您不熟悉 Azure 虛擬網路 (VNETs)，這是讓您控制存取權的非網際網路 routeable 網路中放置許多 Azure 資源的功能。  這些網路然後可連線到您上的部署網路，使用各種不同的 VPN 技術。  若要瞭解更多有關 Azure 虛擬網路開始的資訊︰ [Azure 虛擬網路概觀][VNETOverview]。  

Azure 應用程式服務有兩種格式。  

1. 支援各種資費方案多租用戶系統
1. 部署到您 VNET 應用程式服務環境 (ASE) 進階功能。  

這份文件移到 VNET 整合和應用程式服務環境。  如果您想要進一步瞭解 ASE 功能，然後開始的資訊︰[應用程式服務環境簡介][ASEintro]。

VNET 整合可讓您 web 應用程式存取您的虛擬網路中的資源，但不能從虛擬網路至您的 web 應用程式私人存取。  使用設定內部負載平衡器 (ILB) 與 ASE，才可以使用私用網站存取權。  使用 ILB ASE 的詳細資訊，請從開始的文件︰[建立和使用 ILB ASE][ILBASE]。 

常見的情況，您可以在其中使用 VNET 整合啟用存取您的 web 應用程式至資料庫或 web 服務虛擬機器中 Azure 虛擬網路上執行。  整合 VNET 您不需要公開公用端點的應用程式，在您 VM，但可以改為使用私人的非網際網路舉例來說地址。  

VNET 整合功能︰

- 需要標準或進階版價格計劃 
- 可搭配 Classic(V1) 或資源 Manager(V2) VNET 
- TCP 及 UDP 的支援
- 適用於網頁、 行動電話及 API 應用程式
- 可讓應用程式，一次連接至僅 1 VNET
- 啟用與整合應用程式服務計劃中的最多 5 VNETs 
- 可讓應用程式服務計劃中的多個應用程式所使用相同的 VNET
- 支援因為 VNET 閘道器的依賴 99.9%SLA

有 VNET 整合不支援包括的一些事項︰

- 裝載在磁碟機
- AD 整合 
- NetBios
- 私用網站存取權

### <a name="getting-started"></a>快速入門 ###
以下是您的 web 應用程式連線到虛擬網路之前，請記住的一些事項︰

- VNET 整合只適用於**標準**或**進階版**價格計劃中的應用程式。  如果您啟用的功能，然後在您的應用程式服務方案不受支援的價格方案不按比例縮放您的應用程式會遺失其連線至他們使用 VNETs。  
- 如果您的目標虛擬網路已經存在，您必須先點為網站要有 vpn 才能啟用動態路由閘道器可將其連線至應用程式。  如果您的閘道器設定靜態路由，您無法啟用點為網站虛擬私人網路 (VPN)。
- VNET 必須在同一份訂閱為您的應用程式服務 Plan(ASP)。  
- 與 VNET 整合應用程式會使用該 VNET 指定的 DNS。
- 依預設整合應用程式會只將流量路由傳送至您 VNET 根據您 VNET 中定義的路徑。  


## <a name="enabling-vnet-integration"></a>啟用 VNET 整合 ##

這份文件著重於 VNET 整合使用 Azure 入口網站。  若要您的應用程式使用 PowerShell 啟用 VNET 整合，請遵循以下指示︰[連接您的應用程式虛擬網路使用 PowerShell][IntPowershell]。

您可以選擇將您的應用程式連線到新的或現有的虛擬網路。  如果您建立新的網路，然後除了只建立 VNET 您整合的一部分，動態路由閘道器會為您預先設定，並指向網站 VPN，將會啟用。  

>[AZURE.NOTE] 設定新的虛擬網路整合，可能需要幾分鐘的時間。  

若要啟用 VNET 整合開啟您的應用程式設定，然後選取 [網路。  開啟的 UI 提供三個網路的選項。  本指南只會將 VNET 整合透過混合式部署連線，應用程式服務環境稍後會討論這份文件。  

如果您的應用程式不正確的價格計劃 UI 好心會讓您不按比例縮放到較高的價格方案，您所選擇的計劃。


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>啟用現有的 VNET VNET 整合###
VNET 整合使用者介面，可讓您從您 VNETs 的清單中選取。  傳統 VNETs 會指出，這些是例如 VNET 名稱旁的單字在括弧中的 「 傳統 」。  排序清單，例如資源管理員 VNETs 會列在第一個。  在以下所示的圖像中，您可以看到只有一個 VNET，可以選取。  有多個原因 VNET 會灰色包括︰

- VNET 位於您的帳戶有權存取的另一個訂閱
- 若要啟用網站 VNET 沒有點
- VNET 沒有動態路由閘道器


![][2]

若要啟用整合，只要按一下您想要與整合 VNET 就可以了。  選取 VNET 之後，您的應用程式會自動重新啟動變更才會生效。  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>啟用網站傳統的 VNET 點 #####
如果您 VNET 沒有閘道，也不具有網站的點您必須設定的第一個。  若要這樣做為傳統的 VNET，移至[Azure 入口網站][ AzurePortal] ，並顯示虛擬 Networks(classic) 的清單。  從網路上的這裡按一下您想要與整合，然後按一下大稱為 VPN 連線的基本資訊] 下方的方塊。  從這裡開始，您可以建立您指向網站 VPN，甚至就建立閘道器。  您透過點移至閘道器建立經驗的網站之後，將會關於 30 分鐘才能開始。  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>啟用點資源管理員 VNET 中的網站 #####

閘道器設定資源管理員 VNET，並指向的網站，您需要記錄時，使用 PowerShell[設定點為網站連線至虛擬網路使用 PowerShell][V2VNETP2S]。  若要執行這項功能 UI 尚無法使用。 

### <a name="creating-a-pre-configured-vnet"></a>建立預先設定的 VNET ###
如果您要建立新的 VNET 閘道器設定和點到網站時，應用程式服務網路 UI 有執行這項作業，但僅適用於資源管理員 VNET 的功能。  如果您想要使用的閘道器和點為網站建立傳統的 VNET 您需要透過網路的使用者介面手動執行此動作。 

若要建立資源管理員 VNET 透過 VNET 整合 UI，只要選取 [**建立新的虛擬網路**，並提供:

- 虛擬網路名稱
- 虛擬網路地址區塊
- 子網路名稱
- 子網路地址區塊
- 閘道器地址區塊
- 點-網站地址區塊

如果您要連線到其他網路的任何此 VNET 應避免挑選與這些網路重疊的 IP 位址空間。  

>[AZURE.NOTE] 資源管理員 VNET 建立閘道器即將 30 分鐘以及目前會不 VNET 與整合應用程式。  您 VNET 建立閘道器後需要回來 VNET 整合 UI 應用程式，然後選取您新 VNET。

![][3]

Azure VNETs 通常會建立內私人網路地址。  依預設 VNET 整合功能將任何將流量路由傳送至您 VNET 預定這些 IP 位址範圍。  私用的 IP 位址範圍是︰

- 10.0.0.0/8-這是 10.0.0.0 相同-10.255.255.255
- 172.16.0.0/12-這是 172.16.0.0 相同-172.31.255.255 
- 192.168.0.0/16-這是 192.168.0.0 相同-192.168.255.255
 
VNET 位址空間必須在 CIDR 標記法中加以指定。  如果您不熟悉 CIDR 標記法，則指定使用的 IP 位址和整數，代表網路遮罩的地址區塊的方法。 快速參考，請考慮的 10.1.0.0/24 256 地址，10.1.0.0/25 就是 128 地址。  使用 / 32 IPv4 位址是 1 的地址。  

如果您在此設定 DNS 伺服器資訊的將會設定為您 VNET。  VNET 建立之後，您可以編輯此資訊 VNET 使用者體驗。

當您建立傳統的 VNET 使用 VNET 整合 UI 時，就會建立 VNET 為您的應用程式相同的 [資源] 群組中。 

## <a name="how-the-system-works"></a>系統的運作方式 ##
在幕後此功能會建立在您的應用程式連線至您 VNET 點為網站 VPN 技術上。   Azure 應用程式服務中的應用程式有多個租用戶系統架構無法讓為已完成的虛擬機器佈建直接在 VNET 應用程式。  以點為網站技術上建立我們網路存取，只要主機服務應用程式虛擬機器限制。  網路存取權會進一步限制這些應用程式主機上，讓您的應用程式只能存取他們可存取設定您的網路。  

![][4]
 
如果您還沒有設定 DNS 伺服器與虛擬網路您要使用的 IP 位址。  時使用的 IP 位址，請記住，此功能的主要優點是，可讓您使用網路內部的私人私人的地址。  如果您設定您的應用程式使用公用 IP 位址的其中一個您 Vm，然後您不使用 VNET 整合功能，並透過網際網路通訊。


##<a name="managing-the-vnet-integrations"></a>管理 VNET 整合##

連線和中斷連線至 VNET 功能應用程式層級。  ASP 層級，是會影響 VNET 整合跨多個應用程式的作業。  從顯示的應用程式層級的 UI 您可以在您 VNET 取得詳細資料。  大部分的相同資訊也會顯示在 ASP 層級。  

![][5]

從 [網路功能的狀態] 頁面中，您可以看到是否要將您的應用程式連線到您 VNET。  如果 VNET 閘道向下適用於無論然後原因這會顯示為不連線。  

您現在有層級的 VNET 整合 UI 是您從 ASP 取得的詳細資訊相同的應用程式中提供給您的資訊。  以下是這些項目︰

- VNET 名稱-此連結開啟網路使用者介面
- 位置-這會反映您 VNET 的位置。  若要在另一個位置 VNET 與整合可能是。
- 憑證狀態-沒有認證用來保護應用程式，然後 VNET 之間的連線。  這會反映出的測試，以確保這些同步。
- 閘道器狀態-您的閘道器應該基於任何原因然後應用程式無法存取 VNET 中的資源。  
- VNET 位址空間-這是您 VNET 的 IP 位址空間。  
- 指向 [網站位址空間-這是您 VNET 網站 IP 位址空間的點。  您的應用程式會顯示來自其中一個此位址空間中 IPs 通訊。  
- 網站，網站位址空間-您可以使用網站 Vpn 連線您 VNET 上的部署資源或其他 VNETs。  您應具備的 IP 範圍定義的 VPN 連線會顯示以下設定。
- DNS 伺服器-如果您有設定您的 VNET 然後此處所列的 DNS 伺服器。
- 傳送至 VNET-該處 iPs 是您 VNET 具有路由為定義的 IP 位址的清單。  這裡會顯示那些地址。  

您可以採取一些您 VNET 整合的應用程式檢視中只作業是中斷 VNET 目前已連接到您的應用程式。  若要執行這只要按一下中斷頂端。  此動作不會變更您 VNET。  VNET 和它的設定，包括閘道器維持不變。  如果想要刪除您 VNET 必須先刪除資源，包括閘道器。  

應用程式服務計劃] 檢視中有其他作業的數字。  也存取方式會不同於從應用程式。  達到 ASP 網路 UI 只要開啟 ASP UI，並捲動向下。  有 UI 項目稱為網路功能的狀態。  它會提供您 VNET 整合周圍一些次要詳細資料。  按一下此 UI 上開啟在網路功能狀態 ui。  如果您按一下 [按一下這裡以管理 「 您將會開啟 UI，其中列出此 ASP 中 VNET 整合。

![][6]

ASP 的位置，請務必時查看您的整合與 VNETs 的位置，請記住。  當 VNET 位於另一個位置時您最多可能會發生延遲問題。  

與整合 VNETs 是您的應用程式與整合在此 ASP，您可以擁有多少上多少 VNETs 提醒。  

若要查看每個 VNET 新增詳細資料，只要按一下您感興趣 VNET。  除了詳細資料的已記錄之前您也會看到此 ASP 中所使用的 VNET 的應用程式清單。  

提供解答動作有兩個主要的動作。  首先，新增路由流量至您 VNET 離開您的應用程式的能力。  第二個動作是同步處理的憑證和網路資訊的能力。

![][7]

**路由**如先前所述您 VNET 中所定義為路由用途的導向至您 VNET 從您的應用程式的流量。  雖然提供客戶想要傳送額外的外寄流量從應用程式到 VNET 及，此功能的位置，有幾種使用。  會發生什麼情況的流量後的進位到客戶如何設定其 VNET。  

**憑證**憑證的狀態會反映在檢查執行要驗證的應用程式服務的憑證，我們使用 VPN 連線是還是建議。  啟用 VNET 整合，如果這是第一份整合至該 VNET 從任何應用程式，在此 ASP，則以確保連線的安全性憑證需要的 exchange。  憑證以及我們取得 DNS 設定，而其他類似的項目會描述網路。
如果變更這些憑證或網路資訊會需要按一下 [同步處理網路 」。  **注意**︰ 當您按一下 [同步處理網路]，然後您會導致簡短的資料連線的應用程式與您 VNET 之間。  將不會重新啟動您的應用程式時遺失的連線可能會導致您的網站無法正確。  

##<a name="accessing-on-premise-resources"></a>部署資源存取##

VNET 整合功能的優點之一是，如果您的應用程式可以有權存取您的部署資源從您的應用程式，然後您 VNET 連線到網站 VPN 您開啟部署網路。  此工作，但您可能需要更新路由至網站的 IP 點上的部署 VPN 閘道器的範圍。  當網站 VPN 先設定用來設定讓它的指令碼應該設定包括您指向網站 VPN 路由。  如果您新增點之後的網站 VPN 您建立您的網站 VPN，則您必須手動更新路由。  執行動作的詳細資料而異每閘道器，並不如下所示。  

>[AZURE.NOTE] VNET 整合功能可搭配網站要有 vpn 才能存取部署資源時，目前無法搭配 ExpressRoute VPN 執行相同的動作。  使用 [傳統] 或 [資源管理員 VNET 整合時，也是如此。  如果您需要透過 ExpressRoute 要有 vpn 才能存取資源，您可以使用其可以在您 VNET 執行 ASE。 

##<a name="pricing-details"></a>定價詳細資料##
有幾個價格紅您應該知道的時使用 VNET 整合功能。  有 3 相關的費用，此功能的使用︰

- ASP 價格層需求
- 資料傳輸成本
- VPN 閘道成本。

為您的應用程式，才能使用此功能，他們必須在標準或進階版應用程式服務方案。  您可以在以下這些成本中看到更多詳細資料︰[應用程式服務價格][ASPricing]。 

由於網站 Vpn 的方式點會處理，您隨時可以輸出資料的費用透過 VNET 整合連線即使 VNET 位於相同的資料中心。  若要查看這些費用會看以下︰[傳送定價詳細資料][DataPricing]。  

最後一個項目是 VNET 閘道器的成本。  如果您不需要閘道器，例如網站 Vpn 其他項目您付款的閘道器，以支援 VNET 整合功能。  在以下這些成本有詳細資料︰ [VPN 閘道器價格][VNETPricing]。  

##<a name="troubleshooting"></a>疑難排解##

輕鬆地設定功能時，這不表示您的體驗，將會免費的問題。  您應該發生問題存取您所需的端點上有都是您可以使用以測試連線，從應用程式主控台某些公用程式。  有兩種主控台體驗，您可以使用。  其中一個是從 Kudu 主控台，另一個是您連絡 Azure 入口網站中主控台。  若要從您的應用程式，前往 Kudu 主控台移至 [工具]-> Kudu。  這是移至 [站台名稱] 的相同。 scm.azurewebsites.net。  只要開啟後請移至 [偵錯主控台] 索引標籤。  若要取得 Azure 入口網站裝載主控台，然後從您的應用程式移至 [工具]-> 主控台。  


####<a name="tools"></a>工具####

工具偵測 （ping）、 nslookup 和 tracert 無法透過因為安全性限制主控台。  若要填滿 void 該處已新增的兩個不同的工具。  若要測試 DNS 功能新增名為 nameresolver.exe 的工具。  語法如下︰

    nameresolver.exe hostname [optional: DNS Server]

若要檢查您的應用程式而定主機名稱，您可以使用 nameresolver。  如此一來您可以測試如果您有正確設定您的 DNS 的任何項目，或可能不有權存取您的 DNS 伺服器。

[下一步] 工具可讓您測試的 TCP 連線到主機和連接埠的組合。  這項工具稱為 tcpping.exe，其語法是︰

    tcpping.exe hostname [optional: port]

這項工具會告訴您如果您的特定主機和連接埠連絡，但不是會執行相同的工作有 ICMP 基礎偵測 （ping） 公用程式。  ICMP 偵測 （ping） 公用程式會告訴您是否設定您的主機。  使用 tcpping 您瞭解是否您可以存取特定主機上的連接埠。  


####<a name="debugging-access-to-vnet-hosted-resources"></a>偵錯 VNET 存取裝載資源####

有幾個可以防止您的應用程式與特定主機和連接埠的項目。  大部分的時間就三個項目其中一項︰

- **有防火牆的方式** 如果您使用的方式有防火牆，您會按 TCP 逾時。  在此情況下，這是 21 秒。  若要測試連線使用 tcpping 工具。  TCP 逾時可以是因為許多以外的防火牆，但那里開始。  
- **DNS 是無法存取** DNS 逾時為每個 DNS 伺服器的 3 秒。  如果您有 2 是 6 秒的 DNS 伺服器。  使用 nameresolver DNS 是否能夠運作。  請記住，不會使用您 VNET 設定的 DNS 時，您便無法使用 nslookup。
- **無效的 P2S IP 範圍**指向網站的 IP 範圍必須以 RFC 1918 私用的 IP 範圍 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) 如果範圍以外的使用 IPs 然後項目無法運作。  

如果這些項目不接聽您的問題，請先簡單的項目，例如尋找︰  

- 會顯示在入口網站所設定的閘道器？
- 顯示為要同步的憑證？
- 任何人不必在受影響 Asp 中以 「 同步處理網路 」 變更網路設定？ 

如果您的閘道器向下然後將其備份。  同步處理您的認證是否接著移至您的 VNET 整合的 ASP 檢視，然後按下 [同步處理網路 」。  如果您懷疑已 VNET 設定所做的變更而它不是同步處理，您 Asp 然後移至您的 VNET 整合的 ASP 檢視，並按下 [同步處理網路 」 僅為提醒您 VNET 連線與您的應用程式會簡短的資料。  

如果所有的就沒問題了您需要挖掘有點深入︰

- 是否有任何其他使用 VNET 整合達到相同 VNET 中的資源的應用程式？ 
- 您可以移至 [應用程式主控台並使用 tcpping 達到您 VNET 中的任何其他資源？  

上述其中一項是否均為 true 然後您 VNET 整合沒問題了，而是在其他位置的問題。  這是因為沒有簡單的方法，若要查看為何您無法達到 host （主機）︰ 連接埠是較為困難取得。  原因包括︰

- 您具備防火牆阻止您網站的 IP 範圍指向到應用程式的連接埠存取您主機上。  通常交叉子網路需要公開存取。
- 您的目標主機已關閉
- 您的應用程式當機
- 您有錯誤的 IP 或主機名稱
- 不同的連接埠與您的預期上接聽您的應用程式。  您可以檢查移到該主機上，然後使用 「 netstat aon 「 命令提示字元中。  這會顯示哪些識別碼聆聽什麼連接埠的程序。  
- 您的網路安全性群組中，他們避免存取您的應用程式主機和連接埠從您網站的 IP 範圍指向這種方式設定

請記住，您不知道您點的哪些 IP，因此您需要允許存取整個範圍中的，會使用您的應用程式的網站的 IP 範圍。  

其他偵錯步驟包括︰

- 登入您 VNET 中的另一個 VM，嘗試連絡您資源 host （主機）︰ 連接埠從該位置。  有某些 TCP 偵測 （ping） 公用程式您可以使用這個目的，或甚至使用 telnet，如果需要。  目的以下就是判斷連線是否有其他此 VM 從。 
- 顯示該主機和連接埠從您的應用程式從主控台在另一個 VM 和測試 access 應用程式  
####<a name="on-premise-resources"></a>部署資源####
如果您無法達到內部部署的資源，然後檢查的第一個項目是如果您可以在您 VNET 達到資源。  如果運作的下一個步驟是很簡單的。  在您 VNET VM 需要時，嘗試連絡上的部署應用程式。  您可以使用 telnet 或 TCP 偵測 （ping） 公用程式。  如果您 VM 不達到您開啟的部署資源，請先確定您的網站 VPN 連線運作。  如果運作，請檢查以及上的部署閘道器組態和狀態先前所述的相同項目。  

現在您 VNET 裝載如果 VM 連絡您的部署系統，但您的應用程式無法再的原因是可能下列其中一項︰
- 您而不會與您點到網站上的部署閘道中的 IP 範圍設定
- 您的網路安全性群組會封鎖存取網站的 IP 範圍點
- 您開啟的部署防火牆會封鎖流量導向網站的 IP 範圍從端點
- 必須在基礎的網站流量阻止您點到達上的部署網路您 VNET 使用者定義 Route(UDR)

## <a name="hybrid-connections-and-app-service-environments"></a>混合式連線和應用程式服務環境##
有 3 個啟用 VNET 裝載於資源的存取權的功能。  它們是︰

- VNET 整合
- 混合式連線
- 應用程式服務環境

混合式連線會要求您安裝在您的網路中稱為混合式連線 Manager(HCM) 轉送代理程式。  HCM 必須連接至 Azure 和應用程式。  此方案已從遠端網路上的部署網路例如特別是很好，或甚至其他的雲端裝載網路，因為它不需要網際網路存取端點。  HCM 只會在 Windows 上執行，而且您可以擁有最多 5 提供高可用性執行的執行個體。  混合式連線僅支援 TCP 透過，每個 HC 端點符合特定 host （主機）︰ 連接埠組合。  

應用程式服務環境功能可讓您在您 VNET 執行 Azure 應用程式服務的執行個體。  這個選項可讓您應用程式存取您 VNET 不含任何額外的步驟中的資源。  一些其他應用程式服務環境的優點是，您可以使用 8 核心專用的同事 14 GB 的 RAM。  另一個優點是，您可以不按比例縮放以符合您需求的系統。  不同位置的大小有限您 ASP 多租用戶環境中，於 ASE 您控制多少您想要提供給系統的資源。  提供這份文件的網路焦點解答，其中一項好處您沒有 VNET 整合 ASE 是可以使用 ExpressRoute VPN。  

雖然部分使用大小寫的重疊時，沒有任何這些功能可以取代任何其他。  了解使用哪些功能連結至您的需求，和您要使用的方式。  例如︰

- 如果您是開發人員，只是想執行 Azure 中的網站，並讓該存取在辦公桌工作站資料庫使用簡單的方法就是混合式連線。  
- 如果您想將大量的大型組織中公用網頁內容雲端，並管理您的網路中您想要使用應用程式服務環境，然後。  
- 如果您有數個應用程式服務裝載應用程式中，只是要 VNET 整合為來存取您 VNET 中的資源。  

除了使用案例有一些簡單相關特性。  如果您 VNET 已連線到您上的部署網路，然後使用 [VNET 整合或應用程式服務環境部署資源使用簡單的方法。  相反地，如果您 VNET 未連線到您上的部署網路是更多的費用，設定網站 VPN 與您 VNET 比較安裝 HCM。  

除了功能差異有也價格的差異。  應用程式服務環境功能進階版服務產品但提供最網路設定除了其他強大功能的可能性。  VNET 整合可以使用標準或進階版 Asp，而是完美安全地使用中的多租用戶應用程式服務您 VNET 的資源。  混合式連線目前取決於 BizTalk 帳戶有價格開始免費，然後以取得變得較昂貴的層級根據您所需要的數量。  透過使用跨多個網路時，沒有像混合式部署連線，這可讓您存取超過 100 個不同的網路中的資源的任何其他功能。    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
