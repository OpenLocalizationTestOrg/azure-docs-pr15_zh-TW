<properties
    pageTitle="混合式連線概觀 |Microsoft Azure"
    description="深入了解混合式連線與安全性、 TCP 連接埠、 支援的設定。 MABS WABS。"
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
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>混合式連線概觀
簡介混合式部署連線，列出的受支援的設定，並列出的必要的 TCP 連接埠。


## <a name="what-is-a-hybrid-connection"></a>什麼是混合式連線

混合式連線是 Azure BizTalk 服務的功能。 混合式連線提供簡單且方便的方式來 Azure 應用程式服務 （舊稱網站） 的 Web 應用程式功能和 Azure 應用程式服務 （舊稱行動服務） 的行動應用程式功能連接到您的防火牆後的內部部署資源。

![混合式連線][HCImage]

混合式連線優點包括︰

- Web 應用程式和行動應用程式可存取現有的內部部署資料和服務安全地。
- 多個 Web 應用程式或行動應用程式，可以共用存取內部部署資源的混合式連線。
- 最小的 TCP 連接埠，才能存取您的網路。
- 使用混合式連線的應用程式存取只發佈特定內部部署資源透過混合式連線。
- 可以連線至任何使用靜態 TCP 連接埠，例如 SQL Server 與 MySQL、 HTTP Web Api，大部分的自訂 Web 服務的內部部署資源。

    > [AZURE.NOTE] 使用動態連接埠 （例如 FTP 被動式模式或延伸被動式模式） 的 TCP 型服務目前不支援。 LDAP 也不支援。 LDAP 使用靜態 TCP 連接埠，但也可以使用 UDP。 如此一來，它不支援。

- 可以使用 Web 應用程式 （.NET PHP、 Java、 Python、 Node.js） 與 Mobile 應用程式 （Node.js、.NET） 支援所有架構。
- Web 應用程式和行動應用程式可存取內部部署資源中完全相同的方式如同 Web 或行動應用程式位於您的本機電腦上。 例如，相同連線字串使用內部部署也能在 Azure。


混合式連線也會提供控制項與混合式應用程式，包括存取的企業資源完善的企業系統管理員︰

- 使用群組原則設定，管理員可以允許網路上的 [混合式部署連線，並也指定可以存取的混合式應用程式的資源。
- 在公司網路上的事件和稽核記錄提供完善的混合式連線存取的資源。


## <a name="example-scenarios"></a>範例案例

混合式連線支援下列架構與應用程式組合︰

- .NET framework 存取 SQL Server
- .NET framework HTTP/HTTPS WebClient 服務存取
- SQL Server，MySQL PHP 存取
- SQL Server，MySQL Oracle Java 存取
- Java 存取 HTTP/HTTPS 服務

時存取內部部署的 SQL Server 使用混合式部署連線，請考慮下列動作︰

- SQL Express 名為執行個體必須設定為使用靜態連接埠。 根據預設，SQL Express 具名執行個體使用動態連接埠。
- SQL Express 預設執行個體使用靜態連接埠，但您必須啟用 TCP。 根據預設，不會啟用 TCP。
- 使用叢集或可用性群組時`MultiSubnetFailover=true`模式目前不支援。
- `ApplicationIntent=ReadOnly`目前不支援。
- SQL 驗證可能需要為 Azure 應用程式與內部部署的 SQL server 支援的端對端授權方法。


## <a name="security-and-ports"></a>安全性和連接埠

混合式連線使用安全 Azure 應用程式與內部部署混合式連線管理員連線到混合式連線共用 Access 簽章 (SA) 的授權。 應用程式和內部部署混合式連線管理員會建立個別的連線鍵。 可以換和獨立撤銷這些連線按鍵。

混合式連線提供的應用程式與內部部署混合式連線管理員按鍵順暢和安全通訊群組。

請參閱[建立及管理混合式連線](integration-hybrid-connection-create-manage.md)。

*應用程式授權是分開的混合式連線*。 可以使用任何適當授權的方法。 授權方法取決於支援透過 Azure 雲端及內部部署元件的端對端授權方法。 例如，Azure 應用程式存取內部部署的 SQL Server。 在這個案例中，SQL 授權可能的授權方法支援端對端。

#### <a name="tcp-ports"></a>TCP 連接埠
混合式連線要求僅輸出 TCP 或 HTTP 連線能力，從您私人的網路。 您不需要開啟任何防火牆連接埠或變更您的網路外圍設定為允許任何連入的連線到您的網路。

混合式部署連線，可使用下列 TCP 連接埠︰

連接埠 | 您需要的原因
--- | ---
9350-9354 | 資料傳輸會使用下列連接埠。 服務匯流排轉送管理員探查 9350 來決定是否有空 TCP 連接埠。 如果有的話，其假設連接埠 9352 也是使用。 資料流量介紹連接埠 9352。 <br/><br/>允許傳出連線至下列連接埠。
5671 | 當資料流量使用連接埠 9352 時，會將連接埠 5671 作為控制項頻道。 <br/><br/>允許傳出連線到此連接埠。
80、 443 | Azure 一些資料要求會使用下列連接埠。 此外，如果連接埠 9352 和 5671 不是可用的選項，*然後*連接埠 80 和 443 會使用資料傳輸和控制頻道的後援連接埠。<br/><br/>允許傳出連線至下列連接埠。 <br/><br/>**附註**不建議使用這些後援連接埠為取代的其他 TCP 連接埠。 HTTP/WebSocket 作為的通訊協定，而不是原生 TCP 資料頻道。 它可能會導致較低的效能。



## <a name="next-steps"></a>後續步驟

[建立及管理混合式連線](integration-hybrid-connection-create-manage.md)<br/>
[Azure Web 應用程式連線到內部部署資源](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[從 Azure web 應用程式連線至內部部署的 SQL Server](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>另請參閱

[管理 BizTalk 服務上 Microsoft Azure REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[Biztalk︰ 版本圖表](biztalk-editions-feature-chart.md)<br/>
[建立 BizTalk 服務使用 Azure 入口網站](biztalk-provision-services.md)<br/>
[Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
