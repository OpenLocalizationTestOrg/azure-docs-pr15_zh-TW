<properties
    pageTitle="深入瞭解功能 BizTalk 服務版本 |Microsoft Azure"
    description="比較 BizTalk 服務版本的功能︰ 空閒、 開發人員、 基本、 標準和進階版。 MABS WABS。"
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>Biztalk︰ 版本圖表

Azure Biztalk 提供數種版本。 若要判斷哪一個版本最適合您的情況，而且商務需求使用這份文件。


## <a name="compare-the-editions"></a>比較版本

**免費 （預覽版本）**

可以建立及管理混合式連線。 混合式連線方法可輕鬆地連接內部部署系統，例如 SQL Server Azure 的網站。

**開發人員**

在 X12 並 AS2 包含方便使用交易合作夥伴管理入口網站，與一般 EDI 結構描述可支援的混合式部署連線，EAI 和 EDI 郵件處理和豐富 EDI 處理。 可以建立以閱讀和撰寫郵件任何 HTTP/S、 其餘、 FTP、 WCF 和 SFTP 通訊協定連接在雲端服務的常見 EAI 案例。  使用連線至內部部署 LOB 系統準備使用 SAP、 Oracle eBusiness、 Oracle DB、 Siebel 和 SQL Server 卡。 Visual Studio 工具開發人員中心的環境中使用簡易的開發及部署。 若要開發和測試之用只使用沒有服務等級協定 (SLA) 的限制。

**基本**

在混合式連線、 EAI 橋、 EDI 協議及 BizTalk 介面卡套件連線包含大部分的開發人員功能與增加。 也提供高的顯示狀態，以及可與服務等級協定 (SLA) 不按比例縮放的選項。

**標準**

混合式連線、 EAI 橋、 EDI 協議及 BizTalk 介面卡套件連線，包括所有的基本功能以增加。 也提供高的顯示狀態，以及可與服務等級協定 (SLA) 不按比例縮放的選項。

**進階版**

在混合式連線、 EAI 橋、 EDI 協議及 BizTalk 介面卡套件連線包含與增加的標準功能。 也包含封存、 高可用性和縮放與服務等級協定 (SLA) 的選項。


## <a name="editions-chart"></a>版本圖表
下表列出的差異。

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>免費 （預覽版本）</th>
        <th>開發人員</th>
        <th>基本</th>
        <th>標準</th>
        <th>進階版</th>
</tr>

<tr>
<td><strong>開始價格</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk 服務價格</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure 價格計算機</a></td>
</tr>
<tr>
<td><strong>預設最小的設定</strong></td>
<td>1 的免費單位</td>
<td>1 的開發人員單位</td>
<td>1 的基本單位</td>
<td>1 的標準單位</td>
<td>1 的進階版單位</td>
</tr>
<tr>
<td><strong>縮放比例</strong></td>
<td>沒有小數位數</td>
<td>沒有小數位數</td>
<td>是，1 的基本單位量</td>
<td>是，1 的標準單位量</td>
<td>是，1 的進階版單位量</td>
</tr>
<tr>
<td><strong>允許延展最大值</strong></td>
<td>沒有小數位數</td>
<td>沒有小數位數</td>
<td>8 單位</td>
<td>8 單位</td>
<td>8 單位</td>
</tr>
<tr>
<td><strong>每單位的 EAI 橋</strong></td>
<td>不含的功能</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI、 AS2</strong>
<br/><br/>
包含 TPM 合約</td>
<td>不含的功能</td>
<td>包含。 每單位的 10 個合約。</td>
<td>包含。 每單位的 50 合約。</td>
<td>包含。 每單位的 250 合約。</td>
<td>包含。 每單位的 1000年合約。</td>
</tr>
<tr>
<td><strong>每單位的混合式連線</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>混合式連線的資料傳輸每單位 (GB)</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>內部部署 LOB 系統 BizTalk 卡服務連線</strong></td>
<td>不含的功能</td>
<td>1 的連線</td>
<td>2 的連線</td>
<td>5 的連線</td>
<td>25 的連線</td>
</tr>
<tr>
<td align="left"><strong>支援的通訊協定/系統︰</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>服務匯流排 (SB)</li>
<li>Azure Blob</li>
<li>REST Api</li>
</ul>
</td>
<td>不含的功能</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
</tr>
<tr>
<td><strong>可用性</strong>
<br/><br/>
針對服務等級協定 (SLA)，請參閱<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk 服務價格</a>。
</td>
<td>不含的功能</td>
<td>不含的功能</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
</tr>
<tr>
<td><strong>備份與還原</strong></td>
<td>不含的功能</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
</tr>
<tr>
<td><strong>追蹤</strong></td>
<td>不含的功能</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
</tr>
<tr>
<td><strong>封存</strong><br/><br/>
包含性回條 (NRR)，並下載追蹤的訊息</td>
<td>不含的功能</td>
<td>包含</td>
<td>不含的功能</td>
<td>不含的功能</td>
<td>包含</td>
</tr>
<tr>
<td><strong>使用自訂程式碼</strong></td>
<td>不含的功能</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
</tr>
<tr>
<td><strong>轉換，包括自訂 XSLT 的用途</strong></td>
<td>不含的功能</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
<td>包含</td>
</tr>
</table>

> [AZURE.NOTE] 針對硬體失敗恢復，高可用性表示有多個 Vm 內單一 BizTalk 單位。


## <a name="faqs"></a>常見問題集

#### <a name="what-is-a-biztalk-unit"></a>什麼是 BizTalk 單位？
「 單價 」 是 Azure Biztalk 部署原子層級。 每個版本隨附有不同的計算容量和記憶體單位。 例如基本單位比開發人員更多的計算，標準有更多計算比基本，依此類推。 當您要縮放 BizTalk 服務時，您不按比例縮放單位。

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>什麼是 Biztalk 和 Azure BizTalk VM 之間的差異？
Biztalk 提供建置整合解決方案在雲端，則為 true 的平台為-的-服務 (PaaS) 架構。 PaaS 模型，完全著重在應用程式邏輯並保留所有的基礎結構管理至 Microsoft，包括︰

- 不是需要管理或修補虛擬機器。
- Microsoft 可確保可用性。
- 您可以控制視只要要求更多或較少容量透過 Azure 入口網站的縮放比例。

BizTalk 伺服器上 Azure 虛擬機器提供基礎結構為-的-服務 (IaaS) 架構。 您建立虛擬機器，並設定這些完全依您的內部部署環境，讓您容易無程式碼變更的雲端執行現有的應用程式。 使用 IaaS，您負責還是設定虛擬機器、 管理虛擬機器 （例如，安裝軟體並 OS 修補程式），以及架構的應用程式的可用性。

如果您想要在建立新的整合解決方案的最小化基礎結構的管理投入比，請使用 BizTalk 服務。 如果您想要快速移轉現有的 BizTalk 解決方案或尋找視環境開發及測試 BizTalk Server 應用程式，使用 BizTalk Server Azure 虛擬機器上。

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>什麼是 BizTalk 介面卡服務與混合式連線之間的差異？
Azure BizTalk 服務使用 BizTalk 卡服務。 BizTalk 介面卡服務來連線到內部部署的商務線條 (LOB) 系統使用 BizTalk 介面卡套件。 混合式連線提供 Azure 應用程式，如 Web 應用程式中的功能 Azure 應用程式服務和 Azure 行動服務，連線到內部部署資源的簡單且方便的方式。

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>「 混合式連線的資料傳輸 (GB) 每單位 」 是什麼意思？ 這是每分鐘/小時/日/週/月嗎？ 達到限制時，會發生什麼情況？

每單位的混合式連線成本 BizTalk 服務版本而定。 簡單來說，成本取決於您轉移上的資料量。 例如，每天傳送 10 GB 資料成本小於每天轉接 100 GB。 使用 [BizTalk 服務[價格計算機](https://azure.microsoft.com/pricing/calculator/?scenario=full)，來判斷特定的成本。 一般而言，會強制執行限制每日。 如果您超出的限制，任何 overage 是工資率支付薪資的每 GB 為 $1。

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>我 BizTalk 服務中建立協議，為什麼會橋數向上由兩個，而非只？

每個合約分成兩個不同橋︰ 傳送端通訊橋和接收端通訊橋。

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>當我叫用配額數橋或合約限制時，會發生什麼情況？

您無法部署任何新橋或建立新的協定。 若要部署更多，您需要擴充單位，BizTalk 服務帳戶，或更新的版本升級。

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>如何移轉從一層的 Biztalk 到另一個？

免費版無法移轉或 「 向上' 到另一層，並無法備份及還原到另一層。 如果您需要另一層，請建立一個新 BizTalk 服務使用新的層。 必須重新建立新的 BizTalk 服務中使用免費的版本，包括混合式連線]，建立的任何成品。 

對於剩餘的版本，使用備份與還原從一層的成品您移轉到另一個。 例如，備份 [在標準層中，您成品，再將其還原到進階版層。 [Biztalk︰ 備份及還原](biztalk-backup-restore.md)描述支援的移轉路徑，並列出哪些成品備份。 請注意，混合式連線不會備份。 備份及還原至新的層之後, 您再重新建立混合式連線。  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>為服務中包含 BizTalk 卡服務嗎？ 我要如何收到本軟體？

是的 BizTalk 介面卡 pack BizTalk 介面卡服務所包含的 Azure BizTalk 服務 SDK[下載](http://www.microsoft.com/download/details.aspx?id=39087)。

## <a name="next-steps"></a>後續步驟

若要建立 Azure Biztalk Azure 入口網站中，移至[Biztalk︰ 使用 Azure 入口網站佈建](biztalk-provision-services.md)。 若要開始建立應用程式，請移至[Azure BizTalk 服務](http://go.microsoft.com/fwlink/p/?LinkID=235197)。

## <a name="additional-resources"></a>其他資源
- [使用 [Azure 入口網站佈建的 BizTalk 服務︰](biztalk-provision-services.md)<br/>
- [Biztalk︰ 佈建狀態圖表](biztalk-service-state-chart.md)<br/>
- [Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [Biztalk︰ 備份與還原](biztalk-backup-restore.md)<br/>
- [Biztalk︰ 節流](biztalk-throttling-thresholds.md)<br/>
- [BizTalk 服務︰ 發行者名稱和發行者鍵](biztalk-issuer-name-issuer-key.md)<br/>
- [我要如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
