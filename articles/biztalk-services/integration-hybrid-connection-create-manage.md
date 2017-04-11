<properties 
    pageTitle="建立及管理混合式連線 |Microsoft Azure" 
    description="瞭解如何建立混合式部署連線，管理連線，並安裝混合式連線管理員。 MABS WABS" 
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
    ms.date="10/18/2016" 
    ms.author="ccompy"/>


# <a name="create-and-manage-hybrid-connections"></a>建立及管理混合式連線


## <a name="overview-of-the-steps"></a>步驟概觀
1. 建立混合式連線私人網路中輸入 [**主機名稱**] 或 [內部部署資源的**FQDN** 。
2. 混合式連線的連結將 Azure web 應用程式或 Azure 行動應用程式。
3. 安裝在您的內部部署資源的混合式連線管理員並連線到特定的混合式連線。 Azure 入口網站提供安裝並連線按一下體驗。
4. 管理混合式連線和其連線索引鍵。

本主題列出了這些步驟。 

> [AZURE.IMPORTANT] 若要設定混合式連線結束點的 IP 位址可能是。 如果您使用的 IP 位址，您可能會或可能會根據您的用戶端達到內部部署資源。 混合式連線取決於用戶端執行 DNS 查閱。 在大多數情況下，__用戶端__會是應用程式碼。 如果用戶端不會執行 DNS 查詢，（它不會嘗試解決的 IP 位址，就好像網域名稱 (x.x.x.x)），然後透過混合式連線不會傳送流量。
>
> 例如 （虛擬程式碼） 時，您可以定義**10.4.5.6**為您的內部部署主機︰
> 
> **下列情況的運作方式︰**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **下列情況無法運作︰**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`


## <a name="CreateHybridConnection"></a>建立混合式部署的連線

使用 Web 應用程式**或**使用 BizTalk 服務 Azure 入口網站中可以建立混合式連線。 

**若要建立使用 Web 應用程式的混合式部署連線**，請參閱[連線 Azure Web 應用程式開啟內部部署資源](../app-service-web/web-sites-hybrid-connection-get-started.md)。 您也可以從您的 web 應用程式，也就是最好的安裝混合式連線管理員 (HCM)。 

**若要建立 BizTalk 服務中的混合式連線**︰

1. [Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取**BizTalk 服務**，然後選取您 BizTalk 服務。 

    如果您沒有安裝現有 BizTalk 服務，您可以[建立 BizTalk 服務](biztalk-provision-services.md)。
3. 選取 [**混合式連線**] 索引標籤︰  
![混合式連線] 索引標籤][HybridConnectionTab]

4. 選取 [**建立混合式部署連線**，或選取任務列中的 [**新增**] 按鈕。 輸入下列資訊︰

    屬性 | 描述
--- | ---
名稱 | 混合式連線名稱必須是唯一的而且無法 BizTalk 服務相同的名稱。 您可以輸入任何名稱，但可以使用其目的特定。 範例包括︰<br/><br/>薪資*SQLServer*<br/>SupplyList*SharepointServer*<br/>客戶*OracleServer*
主機名稱 | 輸入完整的主機名稱，只有主機名稱或內部部署資源 IPv4 位址。 範例包括︰<br/><br/>mySQLServer<br/>*mySQLServer*。*網域*。 企業*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*。*yourCompany*.com<br/>10.100.10.10<br/><br/>如果您使用的 IPv4 位址，請注意您用戶端或應用程式的程式碼可能無法解決的 IP 位址。 請參閱本主題頂端記下重要事項。
連接埠 | 輸入內部部署資源的連接埠號碼。 例如，如果您使用的 Web 應用程式，請輸入連接埠 80 或連接埠 443。 如果您使用 SQL Server，請輸入連接埠 1433年。

5. 選取核取記號，以完成安裝。 

#### <a name="additional"></a>其他

- 您可以建立多個混合式連線。 請參閱[Biztalk︰ 版本圖表](biztalk-editions-feature-chart.md)所允許的數目。 
- 建立混合式部署中的每個連線使用一組連線字串︰ 應用程式鍵接聽的傳送和內部部署鍵。 每一組具有主要及次要索引鍵。 


## <a name="LinkWebSite"></a>連結您 Azure 應用程式服務 Web 應用程式或行動應用程式

若要連結的 Web 應用程式或行動應用程式中 Azure 應用程式服務至現有的混合式部署連線，請選取**使用現有的混合式連線**在混合式連線刀。 請參閱[存取內部部署資源使用 Azure 應用程式服務中的混合式連線](../app-service-web/web-sites-hybrid-connection-get-started.md)。

## <a name="InstallHCM"></a>安裝混合式連線管理員內部部署

建立混合式連線之後，請於內部部署資源中安裝混合式連線管理員。 Azure web 應用程式或 BizTalk 服務可以下載它。 BizTalk 服務的步驟︰ 

1. [Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取**BizTalk 服務**，然後選取您 BizTalk 服務。 
3. 選取 [**混合式連線**] 索引標籤︰  
![混合式連線] 索引標籤][HybridConnectionTab]
4. 任務列中，選取 [**內部部署設定**︰  
![內部部署安裝程式][HCOnPremSetup]
5. 選取 [**安裝並設定**可執行或下載內部部署系統上混合式連線管理員。 
6. 選取核取記號，以開始安裝。 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>其他
- 混合式連線管理員可以安裝在以下作業系統︰

    - Windows Server 2008 R2 （.NET Framework 4.5 + 和 Windows Management Framework 4.0 + 必要）
    - Windows Server 2012 (Windows Management Framework 4.0 + 必要)
    - Windows Server 2012 R2


- 安裝混合式連線管理員後，會發生以下情況︰ 

    - 混合式連線裝載於 Azure 自動設定為使用主要的應用程式連線字串。 
    - 在內部部署資源自動設定為使用主要開啟內部部署連線字串。

- 混合式連線管理員必須使用有效的內部部署連線字串的授權。 Azure Web 應用程式或行動應用程式必須使用正確的應用程式連線字串的授權。
- 您可以在另一個伺服器上安裝另一個執行個體的混合式連線管理員來調整混合式連線。 設定內部部署接聽項為第一個內部部署接聽使用相同的地址。 在此情況下的流量會是隨機分配 （循環） 之間的內部部署的 active 接聽。 


## <a name="ManageHybridConnection"></a>管理混合式部署的連線
若要管理您的混合式部署連線，您可以︰

- 使用 Azure 入口網站，請移至您的 BizTalk 服務。 
- 使用[REST Api](http://msdn.microsoft.com/library/azure/dn232347.aspx)。

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>複製/重新產生混合式連線字串

1. [Azure 傳統入口網站](http://go.microsoft.com/fwlink/p/?LinkID=213885)登入。
2. 在左側的功能窗格中，選取**BizTalk 服務**，然後選取您 BizTalk 服務。 
3. 選取 [**混合式連線**] 索引標籤︰  
![混合式連線] 索引標籤][HybridConnectionTab]
4. 選取 [混合式連線]。 任務列中，選取 [**管理連線**︰  
![管理選項][HCManageConnection]

    **管理連線**列出的應用程式和內部部署的連線字串。 您可以複製連線字串，或重新產生使用連線字串中的便捷鍵。 

    **如果您選取 [重新產生**、 使用連接字串內共用便捷鍵會變更。 執行下列動作︰
    - 在 Azure 傳統的入口網站中，選取 [**同步處理索引鍵**Azure 應用程式中。
    - 重新執行**內部部署安裝**。 在重新開啟內部部署安裝時，會自動將內部部署資源設定使用更新的主要連線字串。


#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>使用群組原則來控制混合式連線所使用的內部部署資源

1. 下載[混合式連線管理員系統管理範本](http://www.microsoft.com/download/details.aspx?id=42963)。
2. 解壓縮檔案。
3. 在電腦上修改群組原則，請執行下列動作︰  

    - 複製。ADMX 檔案*%WINROOT%\PolicyDefinitions*資料夾。
    - 複製。ADML 檔案*%WINROOT%\PolicyDefinitions\en-us*資料夾。

複製後，您可以使用群組原則編輯器，若要變更的原則。




## <a name="next"></a>下一步

[Azure Web 應用程式連線到內部部署資源](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[從 Azure Web 應用程式連線至內部部署的 SQL Server](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[混合式連線概觀](integration-hybrid-connection-overview.md)


## <a name="see-also"></a>另請參閱

[管理 BizTalk 服務上 Microsoft Azure REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Biztalk︰ 版本圖表](biztalk-editions-feature-chart.md)  
[建立 BizTalk 服務使用 Azure 傳統的入口網站](biztalk-provision-services.md)  
[Biztalk︰ 儀表板]、 監視器和縮放比例] 索引標籤](biztalk-dashboard-monitor-scale-tabs.md)


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
