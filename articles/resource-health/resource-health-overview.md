<properties
   pageTitle="Azure 資源狀況概觀 |Microsoft Azure"
   description="Azure 資源狀況的概觀"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Azure 資源狀況概觀

Azure 資源狀況是一項服務，公開個別 Azure 資源的狀況，而且會提供當中的指導方針對問題進行疑難排解。 在雲端環境中不能直接存取伺服器或基礎結構元素，資源狀況的目標是減少客戶花費的疑難排解步驟，特別是減少花費的時間，決定如果問題的根目錄奠定內應用程式，或因內 Azure 平台事件的時間。

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>什麼會視為資源，並要怎麼資源狀況決定資源是否正常？ 
資源為使用者建立執行個體的資源類型所提供的服務，例如︰ 虛擬機器、 Web app 或 SQL 資料庫。 

資源狀況依賴訊號發出資源，及/或服務，以決定資源是否正常。 請務必請注意，目前健康狀況的某個特定資源的資源狀況只帳戶輸入而不會考慮整體的健康情況可能影響其他項目。 例如報告的虛擬機器，只計算部分基礎結構的狀態會被視為，也就是網路問題就不會顯示在資源狀況，除非有宣告的服務中斷，在這種情況下，它將會透過刀頂端橫幅。 本文稍後的提供服務中斷的詳細資訊。 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>不同的服務健康狀況儀表板有資源狀況？

資源狀況所提供的資訊是微調比所提供的服務健康狀況儀表板。 SHD 通訊影響的區域中的服務可用性的事件，資源狀況公開至特定的資源相關的資訊，例如，將公開影響虛擬機器、 web 應用程式，或 SQL 資料庫的事件。 比方說，如果節點意外地重新開機，客戶該節點所執行的虛擬機器會能夠取得其 VM 為什麼無法使用一段時間的原因。   

## <a name="how-to-access-resource-health"></a>如何存取資源狀況
如需可透過資源健康情況的服務，有 2 存取資源狀況的方式。

### <a name="azure-portal"></a>Azure 入口網站
Azure 入口網站中的資源狀況刀提供資源的健康情況的詳細的資訊，以及建議的資源的目前健康情況而有所不同的動作。 此刀提供最佳體驗時查詢資源狀況，幫助入口網站內的其他資源的存取權。 如上所述之前，建議的動作，以資源狀況刀設定會根據目前健康情況︰

* 狀況良好的資源︰ 偵測不可能會影響該資源的狀況的問題，因為動作所著重協助疑難排解程序。 例如，它提供疑難排解刀，它提供了如何解決最常見的問題客戶美元指引直接存取。
* 不佳的資源︰ Azure 所造成的問題，刀將會顯示的動作 Microsoft 花費 （或已經） 若要復原的資源。 問題所造成的使用者發起的動作，可採取的動作客戶清單刀會因此解決的問題，復原資源。  

一旦您有登入 Azure 入口網站，有兩種方式存取資源狀況刀︰ 

###<a name="open-the-resource-blade"></a>開啟資源刀
開啟指定的資源資源刀。 在開啟資源刀旁設定刀，按一下以開啟資源狀況刀資源狀況。 

![資源狀況刀](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>說明與支援
開啟 [說明及支援刀按一下右上角的問號，接著選取說明 + 支援。 

**從上方導覽列**

![說明 + 支援](./media/resource-health-overview/HelpAndSupport.png)

按一下 [] 方塊隨即會開啟，就會列出您的訂閱中的資源的所有資源狀況訂閱刀。 每個資源，旁邊有圖示，表示其健康狀況。 按一下每個資源將會開啟資源狀況刀。

**資源健康狀況] 磚**

![資源健康狀況] 磚](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>我的資源健康狀態的意義為何？
有 4 不同的健康狀態，您可能會看到您的資源。

### <a name="available"></a>使用
服務中，可能會影響該資源的可用性的平台偵測不到任何問題。 綠色核取記號圖示來表示。 

![資源](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>無法使用

在此情況下服務偵測進行中的問題會影響此資源，例如節點 VM 執行的位置意外地重新啟動的可用性平台。 紅色的 [警告] 圖示來表示。 其他問題的相關資訊所提供的中間的刀，包括︰ 

1.  若要復原資源花費 Microsoft 什麼動作 
2.  問題，包括預期的解析度時間詳細時程表
3.  建議的使用者動作清單 

![無法使用資源](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>無法使用 – 發起的租用戶的客戶
資源無法使用因為客戶的要求，例如停止資源，或要求重新啟動電腦。 這是藍色資訊圖示來表示。 

![資源無法使用因為使用者發起的動作](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>未知
服務並未收到超過 5 分鐘此資源的相關資訊。 灰色問號圖示來表示。 

請務必記下這不明確的表示有錯誤與資源，因此客戶應該遵循這些建議︰

* 如果資源預期執行，但其狀況資源健康情況中，會設定為未知，沒有任何問題，您可以預期的資源分鐘後更新以良好的狀態。
* 如果有存取資源和其狀況的問題會設定為未知資源健康情況中，這可能會提早表示可能有問題和其他調查應該狀況會更新以良好或不佳直到

![資源健康狀態為未知](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>服務影響事件
資源可能也會受到進行中的服務影響事件，如果橫幅會顯示資源狀況刀頂端。 按一下橫幅上隨即會開啟稽核事件刀，它將會顯示資料的其他資訊。

![資源健康情況可能也會受到 SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>我需要知道資源健康情況還有什麼？

### <a name="signal-latency"></a>訊號延遲
表示摘要資源狀況，可能是最 15 分鐘延遲，這可能會造成資源的目前健康狀態和實際可用性之間的差異。 請務必記住這時，可協助排除不需要花費的時間調查可能的問題。 

### <a name="special-case-for-sql"></a>特殊的大小寫的 SQL 
資源狀況報告 SQL 資料庫，而不是 SQL server 的狀態。 時進行此路由提供更實際狀況圖片，則需要的多個元件和服務應該考量來決定資料庫的狀況。 目前訊號依賴登入資料庫，這表示的狀況接收一般的登入 （包括等接收查詢執行要求） 的資料庫狀態定期隨即出現。 如果不 10 分鐘的時間或多段存取資料庫，就會移動到未知的狀態。 這不表示資料庫是無法使用，只要沒有訊號具有已發出，因為執行的登入。 連線至資料庫，並執行查詢，會發出訊號所需的決定及更新資料庫的健康狀態。

## <a name="feedback"></a>意見反應
我們一律是開啟意見與建議以 ！ 請傳送給我們[建議](https://feedback.azure.com/forums/266794-support-feedback)您。 此外，您可以加入與我們透過[Twitter](https://twitter.com/azuresupport)或[MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure)。
