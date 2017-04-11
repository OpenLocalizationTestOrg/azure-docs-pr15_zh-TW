<properties
   pageTitle="邏輯應用程式安裝內部部署資料閘道 |Microsoft Azure"
   description="如何安裝用於內部部署資料閘道器中的邏輯應用程式的相關資訊。"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>邏輯應用程式安裝的內部部署資料閘道器

## <a name="installation-and-configuration"></a>安裝和設定

### <a name="prerequisites"></a>必要條件

最小值︰

* .NET 4.5 framework
* 64 位元版本 Windows 7 或 Windows Server 2008 R2 的 （或更新版本）

建議︰

* 8 核心 CPU
* 8 GB 的記憶體
* 64 位元版本的 Windows 2012 R2 （或更新版本）

相關的考量事項︰

* 您無法在網域控制站安裝閘道器。
* 您不應閘道器的電腦上安裝，這樣的膝上型電腦，可能會關閉、 睡眠，或未連線到網際網路，因為在這些情況下無法執行的閘道器。 此外，閘道效能可能會降低在無線網路上執行。

### <a name="install-a-gateway"></a>安裝閘道器

您可以取得[的內部部署資料閘道器安裝程式](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409)。

指定做為模式的**內部部署資料閘道**、 登入您的工作或學校帳戶，然後設定新的閘道器或移轉、 還原或接手現有的閘道器。

* 若要設定閘道器，它和**修復金鑰**，請輸入**名稱**，然後按一下或點選**設定**。

    指定包含至少八個字元，修復金鑰，並將它維持在安全的地方。 如果您想要移轉、 還原或接手其閘道器，您會需要此按鍵。

* 若要移轉、 還原或接手現有的閘道器，提供建立閘道器時所指定的復原索引鍵。

### <a name="restart-the-gateway"></a>重新啟動閘道器

閘道器執行的 Windows 服務，就跟其他任何 Windows 服務，您可以啟動，並將其停止多種方式。 例如，您可以使用提高權限的權限閘道器會執行，電腦上開啟命令提示字元，並執行其中一個命令︰

* 若要停止服務，請執行此命令時︰

    `net stop PBIEgwService`

* 若要啟動的服務，請執行此命令時︰

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>將防火牆或 proxy 設定

如需如何提供您的閘道器的 proxy 資訊，請參閱[設定 proxy 設定](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/)資訊。

您可以驗證是否您的防火牆或 proxy]，可能會封鎖連線 PowerShell 提示中執行下列命令。 這會測試連線至 Azure 服務匯流排。 這只測試網路連線而沒有與伺服器雲端服務或閘道器的任何項目。 有助於判斷是否您的電腦實際上能至網際網路。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

結果看起來應該像這個範例。 如果**TcpTestSucceeded**不是 true，您可能會被防火牆封鎖。

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

如果您想要詳盡，請與其列在 [[設定連接埠](#configure-ports)下本主題稍後的替代的**電腦名稱**和**連接埠**值。

防火牆可能也會封鎖 Azure 服務匯流排進行 Azure 資料中心的連線。 如果是這樣，您會想要 whitelist （解除封鎖） 所有您的地區針對這些資料中心的 IP 位址。 您可以取得[以下 Azure IP 位址](https://www.microsoft.com/download/details.aspx?id=41653)的清單。

### <a name="configure-ports"></a>設定連接埠

閘道器建立 Azure 服務匯流排輸出連線。 通訊輸出的連接埠︰ TCP 443 （預設）、 5671，5672，透過 9354 9350。 閘道器不需要輸入的連接埠。

進一步瞭解[混合式解決方案](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)。

| 網域名稱 | 輸出的連接埠 | 描述 |
| ----- | ------ | ------ |
| *。 analysis.windows.net | 443 | HTTPS |
| *。 login.windows.net | 443 | HTTPS |
| *。 servicebus.windows.net |5671 5672 | 進階的訊息佇列通訊協定 (AMQP) |
| *。 servicebus.windows.net | 443、 9350 9354 | TCP （存取控制 token 取得的需要 443） 上的服務匯流排轉送上接聽 |
| *。 frontend.clouddatahub.net | 443 | HTTPS |
| *。 core.windows.net | 443 | HTTPS |
| login.microsoftonline.com | 443 | HTTPS |
| *。 msftncsi.com | 443 | 用來測試閘道器無法連到 Power BI 服務的網際網路連線。 |

如果您需要白色清單 IP 位址，而不是網域，您可以下載及使用[的 Microsoft Azure 資料中心的 IP 範圍清單](https://www.microsoft.com/download/details.aspx?id=41653)。 在某些情況下，Azure 服務匯流排進行連線與 IP 位址，而不是出完整的網域名稱。

### <a name="sign-in-account"></a>登入帳戶

使用者將使用 [，登入公司或學校帳戶。 這是您的組織帳戶。 如果您已註冊 Office 365 產品，未提供您的實際工時電子郵件時，它看起來可能像jeff@contoso.onmicrosoft.com。 您的帳戶，請在雲端服務，便會儲存於租用戶中 Azure Active Directory (AAD)。 在大部分情況下，AAD 帳戶的 UPN 相符的電子郵件地址。

### <a name="windows-service-account"></a>Windows 服務帳戶

內部部署資料閘道器設定為使用 NT SERVICE\PBIEgwService Windows 服務登入認證。 根據預設，其具有右邊的記錄檔以服務。 這是您要安裝的閘道器電腦的內容中。

這不是用來連線到內部部署資料來源或與您登入雲端服務的公司或學校帳戶的帳戶。

##<a name="frequently-asked-questions"></a>常見問題集

### <a name="general"></a>一般

**問題**︰ 閘道器支援哪些資料來源？<br/>
**Answer （回應)**: 到此撰寫時，SQL Server。

**問題**︰ 我需要閘道器在雲端，例如 SQL Azure 中的資料來源嗎？ <br/>
**Answer （回應)**: [否]。 閘道器連線到僅內部部署資料來源。

**問題**︰ 什麼實際的 Windows 服務稱為？<br/>
**Answer （回應)**: 在服務閘道器稱為 Power BI 企業閘道服務。

**問題**︰ 是否有任何連入的連線至閘道器雲端？ <br/>
**Answer （回應)**: [否]。 閘道器會使用 Azure 服務匯流排輸出連線。

**問題**︰ 封鎖輸出連線該怎麼辦？ 我需要開啟什麼？ <br/>
**Answer （回應)**: 請參閱連接埠和閘道器會使用的主機。


**問題**︰ 閘道器必須安裝在同一部電腦，做為資料來源嗎？ <br/>
**Answer （回應)**: [否]。 閘道器會使用所提供的連線資訊的資料來源連線。 閘道器視為這方面的用戶端應用程式。 只要就要都能連線至所提供的伺服器名稱。


**問題**︰ 什麼是資料來源中執行查詢，從 [閘道器的延遲時間？ 什麼是最佳架構？ <br/>
**Answer （回應)**: 若要降低網路延遲，請安裝閘道器的資料來源，盡可能接近。 如果您可以安裝閘道器的實際資料來源，它會最小化推出的延遲。 請考慮資料置。 例如，如果您的服務使用西 US 資料中心，且您有裝載於 Azure VM 的 SQL Server，您需要有 Azure VM 西美國中。 將 [最小化延遲並避免出口 Azure VM 上的費用。


**問題**︰ 是否有任何網路頻寬需求？ <br/>
**Answer （回應)**: 建議有好處理您的網路連線能力。 每個環境都不同，且正在傳送資料量會影響結果。 使用 ExpressRoute 可以協助確保處理量內部部署和 Azure 資料中心之間的層級。

您可以使用協力廠商工具 Azure 速度測試應用程式，以協助評估您處理量的功能。


**問題**︰ 可以透過 Azure Active Directory 帳戶執行的閘道器的 Windows 服務嗎？ <br/>
**Answer （回應)**: [否]。 Windows 服務必須具備有效的 Windows 帳戶。 根據預設，它會執行與服務的 NT SERVICE\PBIEgwService SID。


**問題**︰ 如何結果傳送至雲端？ <br/>
**Answer （回應)**: 這是透過 Azure 服務匯流排。 如需詳細資訊，請參閱運作方式。


**問題**︰ 我的認證儲存？ <br/>
**Answer （回應)**: 您輸入的資料來源的認證儲存加密閘道器雲端服務中。 認證是在閘道器內部解密。

### <a name="high-availabilitydisaster-recovery"></a>高的可用性損壞修復

**問題**︰ 啟用高可用性閘道器的情況下的計劃嗎？ <br/>
**Answer （回應)**: 這是在藍圖，但我們時間表還沒有。


**問題**︰ 會損毀修復提供哪些選項？ <br/>
**Answer （回應)**: 您可以使用 [復原] 鍵，若要還原或移動閘道器。 當您安裝的閘道器時，指定 [復原] 鍵。


**問題**︰ 修復金鑰的優點是什麼？ <br/>
**Answer （回應)**: 提供移轉或復原損毀之後的閘道器設定。

### <a name="troubleshooting"></a>疑難排解

**問題**︰ 閘道器記錄在哪裡？ <br/>
**Answer （回應)**: 請參閱本主題稍後的工具。


**問題**︰ 如何看到查詢會被傳送至內部部署資料來源？ <br/>
**Answer （回應)**: 您可以啟用查詢追蹤，包括所傳送的查詢。 請記得要變更回原始完成疑難排解的值。 離開啟用追蹤的查詢，會導致大記錄。

您也可以查看您的資料來源具有追蹤查詢的工具。 例如，您可以使用延伸事件或 SQL 分析工具，SQL Server Analysis Services。

## <a name="how-the-gateway-works"></a>閘道器的運作方式

當使用者進行互動連線到內部部署資料來源的項目︰

1. 雲端服務會建立查詢，以及加密資料來源的認證，並將查詢傳送到佇列中的閘道器至 [處理程序。
1. 服務分析查詢，並將要求 Azure 服務匯流排推入。
1. 內部部署資料閘道輪詢 Azure 服務匯流排的擱置的要求。
1. 閘道器取得查詢，會將認證，解密並連線到資料來源，使用這些認證。
1. 閘道器會將查詢傳送到執行的資料來源。
1. 回到閘道器，然後拖曳到雲端服務，結果會傳送資料來源的資料。 服務，然後使用的結果。

## <a name="troubleshooting"></a>疑難排解

### <a name="update-to-the-latest-version"></a>更新為最新版本

閘道版本過期時，可以呈現大量的問題。  是好的一般做法，以確定您是最新版本。  如果您還沒有更新閘道器月份，或變長，您可能要考慮安裝最新版本的閘道器，並請參閱如果您可以重現問題。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>錯誤︰ 無法將使用者新增至群組。 （-2147463168 PBIEgwService 效能記錄使用者）

如果您想要安裝網域控制站，不支援的閘道器，您可能會收到此錯誤。 您必須部署不網域控制站的電腦上的閘道器。

## <a name="tools"></a>工具

### <a name="collecting-logs-from-the-gateway-configurator"></a>從 [閘道器設定程式收集記錄

您可以收集閘道器的多個記錄。 先記錄 ！

#### <a name="installer-logs"></a>安裝程式記錄

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>設定記錄

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>企業閘道器服務記錄檔

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>事件記錄檔

資料管理閘道並 PowerBIGateway 記錄會出現在**應用程式及服務記錄檔**。

### <a name="fiddler-trace"></a>Fiddler 追蹤

[Fiddler](http://www.telerik.com/fiddler)是從 Telerik 監視 HTTP 流量的免費工具。  您可以請參閱上一步]，並都會使用 Power BI 服務從用戶端電腦。 這可能會顯示錯誤，以及其他相關的資訊。

## <a name="next-steps"></a>後續步驟
- [建立邏輯應用程式的內部部署連線](app-service-logic-gateway-connection.md)
- [企業整合功能](app-service-logic-enterprise-integration-overview.md)
- [邏輯應用程式連接器](../connectors/apis-list.md)