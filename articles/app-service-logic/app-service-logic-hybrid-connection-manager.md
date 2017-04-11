<properties 
    pageTitle="使用混合式連線管理員 |Microsoft Azure" 
    description="安裝並設定混合式連線管理員連線到內部部署邏輯應用程式中的連接器" 
    services="app-service\logic" 
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/>

# <a name="connect-to-on-premises-connectors-using-the-hybrid-connection-manager"></a>連線到內部部署連接器使用混合式連線管理員

>[AZURE.NOTE] 此版本，請參閱適用於邏輯應用程式 2014年-12-01-預覽結構描述版本。 邏輯應用程式上市 (GA) 連線時使用閘道器的內部部署。 進一步瞭解新增[閘道器](app-service-logic-gateway-connection.md)與[邏輯應用程式 GA](https://azure.microsoft.com/documentation/services/logic-apps/)。

若要使用的內部部署系統，邏輯應用程式會使用混合式連線管理員。 某些連接器可以連線到內部部署系統，例如 SQL Server、 SAP、 SharePoint 和等等。 

混合式連線管理員 (HCM) 是按一下-一次在您的網路，您的防火牆後方 IIS 伺服器已安裝的安裝程式。 使用 Azure 服務匯流排轉送，HCM 驗證與中 Azure 連接器的內部部署系統。 

> [AZURE.NOTE] 如果您連線到內部部署資源，您的防火牆後方需要混合式連線管理員。 如果您不連線到內部部署系統，您就不需要混合式連線管理員。

若要開始，您需要︰

- Azure 服務匯流排轉送命名空間 SA 連接字串。 請參閱判斷哪些層包含轉送的[服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/)。
- 內部部署系統登入資訊，包括使用者名稱和密碼。 例如，如果您連線到內部部署的 SQL Server，您需要的 SQL Server 登入帳戶與密碼。
- 內部部署伺服器資訊，包括連接埠號碼]，然後伺服器名稱。 例如，如果您連線到內部部署的 SQL Server，您需要 SQL 伺服器名稱] 和 [TCP 連接埠號碼。

## <a name="get-the-service-bus-connection-string"></a>取得服務匯流排連線字串

在 Azure 入口網站中，複製服務匯流排根 SA 連線字串。 此連線字串所 Azure 連接器連接到您的內部部署系統。 

1. 在[Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)中，選取您的服務匯流排命名空間，然後選取**連線資訊**︰

    ![][SB_ConnectInfo]

2. 複製 SA 連線字串︰

    ![][SB_SAS]

## <a name="install-the-hybrid-connection-manager"></a>安裝混合式連線管理員

1. 在[Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)中，選取您所建立的連接器。 若要開啟它，您可以選取 [**瀏覽**、 選取**API 應用程式**，然後選取 [連接器] 或 [API 應用程式。 
<br/><br/>
在**混合式連線**的設定不**完整**的︰
<br/>
![][2] 

2. 選取 [**混合式連線**]。 列出您先前輸入的服務匯流排連線字串。
3. 複製**主要設定字串**︰
<br/>
![][PrimaryConfigString]

4. **內部部署混合式連線管理員**] 下您可以下載混合式連線管理員，或將其安裝直接從入口網站。 
<br/><br/>
若要安裝直接從入口網站，請前往內部部署 IIS 伺服器，請瀏覽入口網站，然後選取 [**下載並設定**。
<br/><br/>
若要下載混合式連線管理員，請移至您的內部部署 IIS 伺服器，然後移至**ClickOnce 應用程式**(http://hybridclickonce.azurewebsites.net/install/Microsoft.Azure.BizTalk.Hybrid.ClickOnce.application)。 安裝會自動啟動，您可以執行。

5. **接聽程式安裝**在視窗中，輸入您先前已貼上 （在步驟 3） 的**主要設定字串**，然後選取 [**安裝**]。

安裝完成時，會顯示下列訊息︰
<br/>
![][3] 

現在您瀏覽至連接器一次，混合式連線狀態為**已連線**。 您可能必須關閉連接器並重新開啟時︰
<br/>
![][4] 

> [AZURE.NOTE] 若要切換至第二個連接字串，重新執行混合式連線設定，然後輸入**第二個設定字串**。


## <a name="tcp-ports-and-security"></a>TCP 連接埠與安全性

當您建立的混合式部署連線時，網站會建立本機內部部署 IIS 伺服器上。 IIS 伺服器可 DMZ 中。 IIS 伺服器上的 TCP 連接埠需求包括︰

TCP 連接埠 | 為什麼
--- | ---
 | 沒有內送的 TCP 連接埠是必要。
9350-9354 | 資料傳輸會使用下列連接埠。 服務匯流排轉送管理員探查 9350 來決定是否有空 TCP 連接埠。 如果有的話，其假設連接埠 9352 也是使用。 資料流量介紹連接埠 9352。 <br/><br/>允許傳出連線至下列連接埠。
5671 | 連接埠 5671 資料流量使用連接埠 9352 時，會用來作為控制頻道。 <br/><br/>允許傳出連線到此連接埠。 
80、 443 | 如果連接埠 9352 和 5671 不是可用的選項，*然後*連接埠 80 和 443 是使用的資料傳輸和控制頻道的後援連接埠。<br/><br/>允許傳出連線至下列連接埠。
內部部署系統連接埠 | 在內部部署系統上，開啟 [系統所使用的連接埠。 例如，SQL Server 通常是使用連接埠 1433年。 開啟這個 TCP 連接埠。

[使用服務匯流排防火牆主機服務](http://msdn.microsoft.com/library/azure/ee706729.aspx)

## <a name="troubleshooting"></a>疑難排解

![][HCMFlow]

### <a name="on-premises-troubleshooting"></a>內部部署的疑難排解

1. IIS 在伺服器上，確認已安裝 IIS 網站角色啟動所有 IIS 服務。
2. 在 IIS 伺服器上，確認混合式連接管理員是安裝及執行︰
 - 在 IIS 管理員 (inetmgr) 中***MicrosoftAzureBizTalkHybridListener***網站應該會列出，並執行。 
 - 此網站使用***HybridListenerAppPool***所執行的*其他*區域內建的使用者帳戶。 此 AppPool 應該也要開始。
3. 在 IIS 伺服器上，確認連接器安裝及執行︰ 
 - 網站建立的連接器。 例如，如果您建立 SQL 連接器，是***MicrosoftSqlConnector_nnn***網站。 在 IIS 管理員 (inetmgr)，確認此網站會列出，並啟動。 
 - 此網站使用名為***HybridAppPoolnnn***自己 IIS 應用程式集區。 此 AppPool 執行的*其他*區域內建的使用者帳戶。 此網站和 AppPool 應該同時啟動。 
 - 瀏覽本機的連接器。 例如，如果您連接器的網站是使用連接埠 6569，瀏覽至 http://localhost:6569 中。 未設定預設文件，以便`HTTP Error 403.14 - Forbidden error`預期。
4. 在您的防火牆，請確認此主題中所列的 TCP 連接埠是開啟。
5. 查看的來源或目標系統︰
 - 某些內部部署系統需要額外的相依性的檔案。 例如，如果您連線到內部部署 SAP 中，有些其他 SAP 檔案必須安裝 IIS 伺服器上。
 - 請檢查系統與登入帳戶的連線。 例如，使用系統的 TCP 連接埠必須開啟，例如 SQL Server 的連接埠 1433年。 在 [Azure 入口網站中所輸入的登入帳戶必須具備系統的存取權。
6. 在 IIS 伺服器上，核取任何錯誤事件記錄檔。 
7. 清除並重新安裝混合式連線管理員︰ 
 - 在 IIS 中，請手動刪除連接器網站和其應用程式集區。 
 - 重新執行混合式連線管理員，並確認您輸入正確的**主要設定字串**的連接器。



### <a name="in-the-azure-classic-portal"></a>Azure 傳統入口網站中

1. 確認服務匯流排命名空間有**作用中**狀態。
2. 當您建立連接器時，請輸入服務匯流排 SA 連線字串。 不要輸入 [ACS 連線字串。


## <a name="faq"></a>常見問題集

**問題**︰ 有兩個的混合式連線管理員。 有何差別？ 

**Answer （回應)**: 有主要由 Web 應用程式 （舊稱網站） 與 Mobile 應用程式 （舊稱行動服務） 用來連線到內部部署[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)技術。 此混合式連線管理員是它自己的[設定](../biztalk-services/integration-hybrid-connection-create-manage.md)和使用 Azure BizTalk 服務 （在幕後）。 TCP 及 HTTP 通訊協定只支援。

Azure 應用程式服務連接器，我們也有混合式連線管理員。  此混合式連線管理員會*的使用 Azure BizTalk 服務 （幕後作業）*和支援多個 TCP 和 HTTP 通訊協定。 請參閱[連接器與 API 的應用程式清單](app-service-logic-connectors-list.md)。

同時使用 Azure 服務匯流排連線到內部部署系統。

**問題**︰ 當我建立自訂的 API 應用程式時，我可以使用應用程式服務混合式連線管理員連線到內部部署？ 

**Answer （回應)**: 不在傳統的意義。 您可以使用內建的連接器，應用程式服務混合式連線管理員設定為連線到內部部署系統。 然後，使用您自訂的 API 應用程式，可能使用邏輯的應用程式中使用此連接器。 目前您無法開發，或建立您自己混合式部署 API 應用程式 （例如 SQL 連接器] 或 [檔案連接器中）。

如果您自訂的 API 使用 TCP 或 HTTP 的連接埠，您可以使用[混合式連線](../biztalk-services/integration-hybrid-connection-overview.md)和其混合式連線管理員。 在這個案例中，使用 Azure BizTalk 服務。 [連線到內部部署的 SQL Server 從 web 應用程式](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)可以幫助。  


## <a name="read-more"></a>了解更多

[監控您的邏輯應用程式](app-service-logic-monitor-your-logic-apps.md)<br/>
[服務匯流排價格](https://azure.microsoft.com/pricing/details/service-bus/)



[SB_ConnectInfo]: ./media/app-service-logic-hybrid-connection-manager/SB_ConnectInfo.png
[SB_SAS]: ./media/app-service-logic-hybrid-connection-manager/SB_SAS.png
[PrimaryConfigString]: ./media/app-service-logic-hybrid-connection-manager/PrimaryConfigString.png
[HCMFlow]: ./media/app-service-logic-hybrid-connection-manager/HCMFlow.png
[2]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-hybrid-connection-manager/HybridSetup.jpg
[4]: ./media/app-service-logic-hybrid-connection-manager/BrowseSetupComplete.jpg

 
