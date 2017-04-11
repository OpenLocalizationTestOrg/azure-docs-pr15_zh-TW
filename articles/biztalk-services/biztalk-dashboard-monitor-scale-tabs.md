<properties 
    pageTitle="儀表板，監視器、 縮放比例] 設定，並 BizTalk 服務中的混合式連線 |Microsoft Azure" 
    description="深入了解控制項，並監控 Biztalk 傳統入口網站] 索引標籤上的效能︰ 儀表板、 監控、 小數位數、 設定及混合式連線。 MABS WABS" 
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
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>檢閱 [儀表板、 監控、 小數位數、 設定及混合式連線] 索引標籤

建立您的 BizTalk 服務後，當您部署應用程式時，您可以變更的部分 BizTalk 服務設定，並監控應用程式的效能。 

當您開啟 Azure 傳統入口網站時，您會自動放在 [**所有項目**] 索引標籤。 若要檢視您的 BizTalk 服務，**所有項目**] 索引標籤中，選取您 BizTalk 服務，或選取 [ **BIZTALK 服務**] 索引標籤。然後選取您 BizTalk 服務的名稱。

如此會在新視窗開啟下列索引標籤。 本主題說明下列索引標籤。

## <a name="quick-start-quick-startquickstart"></a>快速入門 (![快速入門][QuickStart])
根據 BizTalk Services Edition 中，列出的所有選項可能無法使用。 
<table border="1">
    <tr>
        <td><strong>取得工具</strong></td>
        <td>下載 BizTalk 服務 SDK 內部開發的電腦上安裝的 Visual Studio 專案範本。 <strong>BizTalk 服務</strong>（橋） 和<strong>BizTalk 服務成品</strong>（轉換） Visual Studio 專案部署您 BizTalk 的服務，就會建立這些範本。
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">我要如何開始使用 Azure BizTalk Services SDK</a>以及<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">安裝 Azure BizTalk Services SDK</a>列出的步驟，即可開始使用。
        </td>
    </tr>
    <tr>
        <td><strong>建立合作夥伴合約</strong></td>
        <td>隨後便會開啟 [裝載於 Azure 其中加入合作夥伴和建立 X12，AS2 Azure BizTalk Services 入口網站和 EDIFACT EDI 合約。
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">設定元件 EDI 訊息 BizTalk Services 入口網站上</a>列出的步驟，即可開始使用。
        </td>
    </tr>

<tr>
        <td><strong>深入瞭解 BizTalk 服務</strong></td>
        <td>移至<a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">學習中心</a>若要進一步瞭解 Azure BizTalk 服務。</td>
</tr>
</table>


在底部的工作列，您可以︰

<table border="1">

<tr>
<td><strong>管理</strong>應用程式部署</td>
<td>隨即會開啟 [Azure Biztalk 入口網站。 BizTalk Services 入口網站是 EDI 設定，包括新增合作夥伴和建立 X12，AS2 進入和 EDIFACT 合約。
<br/><br/>
這是<strong>建立合作夥伴合約</strong>相同<strong>快速入門</strong>] 索引標籤上。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">設定元件 EDI 訊息 BizTalk Services 入口網站上</a>BizTalk Services 入口網站上提供的更多資訊。</td>
</tr>

<tr>
<td><strong>連線資訊</strong>的 Access 控制項的命名空間</td>
<td>選取 [連接資訊]，然後 Access 控制項的命名空間、 預設簽發者與預設鍵會顯示。 您可以將這些值，複製。
<br/><br/>
您也可以開啟 Access 控制項入口網站。 <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">建立存取控制命名空間</a>存取控制入口網站上提供的更多資訊。</td>
</tr>

<tr>
<td><strong>同步處理鍵</strong>儲存帳戶</td>
<td>當您建立的儲存空間帳戶時，主索引鍵和次要鍵會自動建立。 下列的加密金鑰控制存取您儲存的帳戶。 您 BizTalk 服務會自動使用主索引鍵。 <strong>同步處理鍵</strong>可讓使用者不會影響 BizTalk 服務主索引鍵和 [次要鍵之間切換。
<br/><br/>
例如，您想 BizTalk 服務儲存帳戶使用新的主索引鍵。 若要執行這項操作︰
<br/><br/>
<ol>
<li>選取您 BizTalk 服務，然後選取 [<strong>同步處理索引鍵</strong>。 選取第二個機碼。 當您執行此動作時，BizTalk 啟動服務使用第二個機碼。</li>
<li>在 Azure 傳統的入口網站中，選取您儲存的帳戶，然後重新產生主索引鍵]。 請記住，您 BizTalk 服務會使用第二個機碼。</li>
<li>選取您 BizTalk 服務，然後選取 [<strong>同步處理索引鍵</strong>。 現在，選取主索引鍵。 這是您重新產生新的主索引鍵。</li>
<li>在 Azure 傳統的入口網站中，選取您儲存的帳戶，然後重新產生金鑰次要]。</li>
</ol>
<br/>
此程序稱為 「 變換鍵 」。 若要讓使用者不會影響 BizTalk 服務切換主索引鍵和次要用途。</td>
</tr>

<tr>
<td><strong>刪除</strong>應用程式</td>
<td>當您選取 [刪除 BizTalk 服務及部署的所有項目將會移除。</td>
</tr>
</table>


## <a name="dashboard"></a>儀表板
根據 BizTalk Services Edition 中，列出的所有選項可能無法使用。 

當您選取 BizTalk 服務名稱時，則會顯示 [儀表板] 索引標籤。 在儀表板，您可以︰

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>使用概觀︰ 顯示使用混合式連線數目
在 GB，也會顯示資料使用方式。 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>公制的圖表︰ 顯示固定的效能度量值清單
這些計量提供有關 BizTalk 服務的健康狀況的即時值。 您也可以選擇的**相對**或**絕對**的值和時間範圍內的圖表] 中會顯示指標的**間隔**。 

這些效能指標的說明，請移至[使用指標](#Metrics)本主題中。


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>快速檢視︰ 列出您 BizTalk 服務的內容

<table border="1">

<tr>
<td><strong>更新追蹤資料庫的認證</strong></td>
<td>變更使用者名稱和密碼登入追蹤資料庫。</td>
</tr>
<tr>
<td><strong>更新 SSL 憑證</strong></td>
<td>可以更新 BizTalk 服務，以便使用不同的 SSL 憑證。 自我簽署的 SSL 憑證就會自動建立當您<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">建立 BizTalk 服務</a>。</td>
</tr>
<tr>
<td><strong>下載憑證</strong></td>
<td>您可以下載至本機電腦您 BizTalk 服務所使用的 SSL 憑證。</td>
</tr>
<tr>
<td><strong>狀態</strong></td>
<td>顯示您 BizTalk 服務的目前狀態。 請參閱<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">Biztalk︰ 服務狀態圖表</a>。 </td>
</tr>
<tr>
<td><strong>服務 URL</strong></td>
<td>您 BizTalk 服務的 URL。 這是相同建立 BizTalk 服務時，輸入<strong>網域 URL</strong> 。</td>
</tr>
<tr>
<td><strong>(VIP) 公用虛擬 IP 位址</strong></td>
<td>指派給 BizTalk 服務 IP 位址。 它適用於所有輸入端點，而且是外寄流量的來源地址。 這個 IP 位址屬於您 BizTalk 服務，只要建立。 如果您刪除 BizTalk 服務，IP 位址指派到另一個 BizTalk 服務。</td>
</tr>
<tr>
<td><strong>ACS 命名空間</strong></td>
<td>驗證 BizTalk 服務。</td>
</tr>
<tr>
<td><strong>版本</strong></td>
<td>清單建立 BizTalk 服務時輸入版本。</td>
</tr>
<tr>
<td><strong>位置</strong></td>
<td>顯示裝載您 BizTalk 服務的地理區域。</td>
</tr>
<tr>
<td><strong>建立</strong></td>
<td>顯示日期及時間 BizTalk 服務的建立。</td>
</tr>
<tr>
<td><strong>追蹤資料庫</strong></td>
<td>Azure SQL 資料庫名稱，儲存您 BizTalk 服務所使用的追蹤資料表。 
<br/><br/>追蹤資料庫
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">需求說明</a>提供詳細資料。</td>
</tr>
<tr>
<td><strong>監控/封存的儲存空間</strong></td>
<td>儲存您的 BizTalk 服務監控輸出 Azure 儲存體帳戶名稱。
<br/><br/>儲存帳戶
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">需求說明</a>提供詳細資料。</td>
</tr>
<tr>
<td><strong>訂閱名稱</strong></td>
<td>列出裝載您 BizTalk 服務的訂閱。 訂閱管理 Azure 傳統入口網站的存取。</td>
</tr>
<tr>
<td><strong>訂閱識別碼</strong></td>
<td>建立訂閱時，會自動產生的訂閱識別碼。 使用時 REST Api，您可能需要輸入訂閱 id。</td>
</tr>
</table>

[Biztalk︰ 佈建使用 Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=302280)清單建立 BizTalk 服務的步驟。


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>管理連線資訊，同步處理鍵和刪除任務列中︰

<table border="1">

<tr>
<td><strong>管理</strong>應用程式部署</td>
<td>隨即會開啟 [Azure BizTalk Services 入口網站。 BizTalk Services 入口網站是 EDI 設定，包括新增合作夥伴和建立 X12，AS2 進入和 EDIFACT 合約。
<br/><br/>
這是<strong>建立合作夥伴合約</strong>相同<strong>快速入門</strong>] 索引標籤上。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">設定元件 EDI 訊息 BizTalk Services 入口網站上</a>BizTalk Services 入口網站上提供的更多資訊。</td>
</tr>
<tr>
<td><strong>連線資訊</strong>的 Access 控制項的命名空間</td>
<td>顯示 Access 控制項的命名空間、 預設的發行者及預設金鑰值;這可以複製。
<br/><br/>
您也可以開啟 Access 控制項入口網站。 此存取控制入口網站是使用 Active Directory 選項，在左側的功能窗格中的相同。
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">管理您的 ACS 命名空間</a>提供存取控制入口網站上的詳細資訊。</td>
</tr>
<tr>
<td><strong>同步處理鍵</strong>儲存帳戶</td>
<td>當您建立的儲存空間帳戶時，主索引鍵和次要鍵會自動建立。 下列的加密金鑰控制存取您儲存的帳戶。 您 BizTalk 服務會自動使用主索引鍵。 <strong>同步處理鍵</strong>可讓使用者不會影響 BizTalk 服務主索引鍵和 [次要鍵之間切換。
<br/><br/>
例如，您想 BizTalk 服務儲存帳戶使用新的主索引鍵。 若要執行這項操作︰
<br/><br/>
<ol>
<li>選取您 BizTalk 服務，然後選取 [<strong>同步處理索引鍵</strong>。 選取第二個機碼。 當您執行此動作時，BizTalk 啟動服務使用第二個機碼。</li>
<li>在 Azure 傳統的入口網站中，選取您儲存的帳戶，然後重新產生主索引鍵]。 請記住，您 BizTalk 服務會使用第二個機碼。</li>
<li>選取您 BizTalk 服務，然後選取 [<strong>同步處理索引鍵</strong>。 現在，選取主索引鍵。 這是您重新產生新的主索引鍵。</li>
<li>在 Azure 傳統的入口網站中，選取您儲存的帳戶，然後重新產生金鑰次要]。</li>
</ol>
<br/>
此程序稱為 「 變換鍵 」。 若要讓使用者不會影響 BizTalk 服務切換主索引鍵和次要用途。</td>
</tr>

<tr>
<td><strong>刪除</strong>應用程式</td>
<td>會移除您 BizTalk 服務及部署的所有項目。</td>
</tr>
</table>


## <a name="monitor"></a>監視器
不適用於免費版。

當您選取 BizTalk 服務名稱時，[監視器] 索引標籤可用，並會顯示下列︰

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>公制的圖表︰ 會顯示所選的效能指標
這些計量提供有關 BizTalk 服務的健康狀況的即時值。 您選擇的效能指標會顯示。 六個效能指標最多可以同時顯示。 

您也可以選擇的**相對**或**絕對**值，會顯示指標的**間隔**時間範圍。 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>若要移除，或在圖形中顯示指標︰
1. 選取 [**監視器**] 索引標籤。
2. 選取 [**新增指標**工作列中︰  
![選取 [新增指標][AddMetrics]
3. 核取您要顯示的效能指標。
4. 選取核取記號，若要返回 [**監視器**] 索引標籤。
5. 選取圖表] 中顯示的度量值公制] 旁的圓形。  

    例如， **CPU 使用率**公制會呈現灰色。輸出不會顯示圖表] 中︰  
![CPU 使用率公制會呈現灰色][GrayedMetric]  

    選取 [灰色出圓形以啟用圖表] 中顯示其輸出的**CPU 使用率**公制︰  
![已啟用 CPU 使用率公制][EnabledMetric]

6. 若要移除顯示圖表和清單的度量單位，請選取 [**刪除公制**工作列中。 若要新增至清單的公制後，選取任務列中的 [**新增指標**、 核取公制，然後選取核取記號，若要返回 [**監視器**] 索引標籤。 選取 [啟用公制圓形出灰色]。

## <a name="Metrics"></a>可用的指標
可使用下列效能計數器/指標︰

<table border="1">

<tr>
<td><strong>RountdTrip 延遲</strong></td>
<td>在所有橋顯示平均時間以毫秒為單位 （毫秒） 處理的時間，直到郵件處理完畢，BizTalk 服務接收訊息的訊息。 會計算成功處理的郵件。<br/><br/>
下列事件發生時，會建立時間戳記︰
<ul>
<li>郵件進入 [閘道器</li>
<li>郵件路由至的目的地</li>
<li>收到目的地回應</li>
<li>傳送至 [閘道器的目的地認可回應</li>
</ul>
<br/>
此計量顯示結果的計算方式如下︰
<br/><br/>
[目的地認可回覆傳送至 [閘道器]-[郵件進入閘道器]</td>
</tr>
<tr>
<td><strong>在來源失敗</strong></td>
<td>顯示失敗的郵件總數 BizTalk 服務時抽取從來源端點的郵件。</td>
</tr>
<tr>
<td><strong>CPU 使用率</strong></td>
<td>列出所有角色執行個體的平均 %處理器時間。</td>
</tr>
<tr>
<td><strong>處理程序延遲</strong></td>
<td>顯示的平均時間以毫秒為單位 （毫秒） 處理郵件 BizTalk 服務過所有橋，不包括在目的地所花費的時間。 會計算成功處理的郵件。<br/><br/>
每個下列事件發生時，會建立時間戳記︰

<ul>
<li>郵件進入 [閘道器</li>
<li>郵件路由至的目的地</li>
<li>收到目的地回應</li>
<li>傳送至 [閘道器的目的地認可回應</li>
</ul>
<br/>此計量顯示結果的計算方式如下︰<br/><br/>
[目的地認可回應傳送至 [閘道器]-[郵件進入閘道器]-[收到目的地回應] + [會郵件路由傳送至目的地]</td>
</tr>
<tr>
<td><strong>在 [程序失敗</strong></td>
<td>顯示總期間 BizTalk 服務處理過所有橋失敗的時間間隔內的訊息數。</td>
</tr>
<tr>
<td><strong>傳送的郵件</strong></td>
<td>顯示總 BizTalk 服務上所有的橋傳送時間間隔內的訊息數。 從管線傳送的郵件到達路由目的地時，就會增加此公制。 此計量不表示郵件已成功處理。<br/><br/>
在邀請回覆案例中，傳送目的地傳送回條應答回到管線時，就會增加度量。</td>
</tr>
<tr>
<td><strong>收到的郵件</strong></td>
<td>顯示總 BizTalk 服務接收所有橋過的時間間隔內的訊息數。 在 [新郵件收到的管線時，就會增加此計量。</td>
</tr>
<tr>
<td><strong>在 [程序中的郵件</strong></td>
<td>顯示總目前正在處理的 BizTalk 服務的時間間隔內的訊息數。</td>
</tr>
<tr>
<td><strong>郵件處理</strong></td>
<td>顯示總的數字的成功 BizTalk 服務處理過所有橋時間間隔內的訊息。 當郵件順利接收到的管線成功路由傳送至目的地，就會增加此公制。</td>
</tr>
</table>


## <a name="scale"></a>縮放比例
在 [縮放比例] 索引標籤中，您可以加上或減去您 BizTalk 服務所使用的單位數量。 根據預設，是一單位設定。 其他單位可以新增至您的 BizTalk 服務不按比例縮放。 當您增加小數位數時，您會增加處理量。 資源的量也會增加，包括部署的橋、 協議、 LOB 連線及處理能力。 例如，您增加從 1 單位刻度 2 的單位。 在此情況下，可以部署雙橋數、 按兩下協議、 按兩下 LOB 連線]，並按兩下處理 power。

某些 BizTalk 版本不提供的縮放比例選項。 在此情況下，允許一個單位。 若要判斷您 edition 可以依比例縮放的單位數量，請參閱[Biztalk︰ 版本圖表](biztalk-editions-feature-chart.md)。

增加單位數量可能會影響價格。 如果您增加單位，請選取 [**儲存**會顯示一則訊息，告訴您如果計費會受到影響。 您選擇要繼續。 當您增加從 Active BizTalk 服務狀態變更，更新為單位的數字。 在 [更新狀態，您 BizTalk 服務會繼續執行。

[Biztalk︰ 版本圖表](biztalk-editions-feature-chart.md)定義 「 單位 」。


## <a name="configure"></a>設定
不適用於混合式連線。

備份狀態設定為 [無] 或 [自動。 設定為 [無] 時，不會自動建立備份。 您設定為自動時，設定備份位置，也就是將備份檔案的頻率備份，以及多久。 

[Biztalk︰ 備份及還原](biztalk-backup-restore.md)提供詳細資料。 


## <a name="HybridConnections"></a>混合式連線
混合式連線連線到內部部署資源的使用靜態 TCP 連接埠，例如 SQL Server 與 MySQL、 HTTP Web Api，大部分的自訂 Web 服務的 Azure 的應用程式，例如 Web 應用程式或在 Azure 應用程式服務中的行動應用程式。 BizTalk 服務中，可在 Azure 傳統的入口網站管理混合式連線。

若要建立 Azure 應用程式服務中的混合式部署連線，請參閱[存取內部部署資源使用 Azure 應用程式服務中的混合式連線](../app-service-web/web-sites-hybrid-connection-get-started.md)。

若要建立或管理 Azure BizTalk 服務中的混合式部署連線，請參閱[混合式連線](integration-hybrid-connection-overview.md)。



## <a name="next"></a>下一步
現在，您已經熟悉不同的索引標籤，您可以進一步瞭解 Azure Biztalk 功能︰

- [Biztalk︰ 節流](biztalk-throttling-thresholds.md)  
- [BizTalk 服務︰ 發行者名稱和發行者鍵](biztalk-issuer-name-issuer-key.md)  
- [BizTalk 服務︰ 備份與還原](biztalk-backup-restore.md)

## <a name="see-also"></a>另請參閱
- [混合式連線](integration-hybrid-connection-overview.md)  
- [Biztalk︰ 開發人員、 基本、 標準和進階版圖表](biztalk-editions-feature-chart.md)  
- [Biztalk︰ 佈建使用 Azure 傳統入口網站](biztalk-provision-services.md)  
- [Biztalk: BizTalk 服務狀態圖表](biztalk-service-state-chart.md)  
- [我要如何開始使用 Azure BizTalk 服務 SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
