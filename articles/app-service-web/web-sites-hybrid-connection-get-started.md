<properties 
    pageTitle="使用 Azure 應用程式服務中的混合式連線存取內部部署資源" 
    description="建立 web app 中 Azure 應用程式服務與使用靜態的 TCP 連接埠的內部部署資源之間的連線" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/03/2016" 
    ms.author="cephalin"/>

#<a name="access-on-premises-resources-using-hybrid-connections-in-azure-app-service"></a>使用 Azure 應用程式服務中的混合式連線存取內部部署資源

您可以在任何使用靜態 TCP 連接埠，例如 SQL Server 與 MySQL、 HTTP Web Api，大部分的自訂 Web 服務的內部部署資源連線 Azure 應用程式服務應用程式。 本文將示範如何建立混合式之間的連線的應用程式服務與內部部署的 SQL Server 資料庫。

> [AZURE.NOTE] 只在[Azure 入口網站](https://portal.azure.com)中使用 Web 應用程式的部分混合式連線功能。 若要建立 BizTalk 服務的連線，請參閱[混合式連線](http://go.microsoft.com/fwlink/p/?LinkID=397274)。 
> 
> 此內容也適用於 Azure 應用程式服務中的行動應用程式。 

## <a name="prerequisites"></a>必要條件
- Azure 的訂閱。 如需可用的訂閱，請參閱[Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。 
 
    如果您想要開始使用 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)，可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 必要; 沒有信用卡沒有承諾。

- 若要使用的內部部署的 SQL Server 或 SQL Server Express 資料庫混合式部署連線，TCP/IP 必須啟用靜態連接埠。 SQL Server 上使用預設的執行個體建議，因為它會使用靜態連接埠 1433年。 安裝及設定混合式連線搭配使用的 SQL Server Express 的資訊，請參閱[連線至內部部署的 SQL Server Azure 網站的使用混合式連線](http://go.microsoft.com/fwlink/?LinkID=397979)。

- 在您安裝本文稍後所述的內部部署混合式連線管理員代理程式的電腦︰

    - 必須為能透過連接埠 5671 連線至 Azure
    - 必須能夠連*主機名稱*︰ 內部部署資源的*portnumber* 。 

> [AZURE.NOTE] 本文中的步驟假設您使用的從會裝載內部部署混合式連線代理程式的電腦的瀏覽器。


## <a name="create-a-web-app-in-the-azure-portal"></a>Azure 入口網站中建立 web 應用程式 ##

> [AZURE.NOTE] 如果您已經在您想要使用此教學課程 Azure 入口網站中建立的 web 應用程式或行動應用程式後端，您可以跳到[建立混合式連線和 BizTalk 服務](#CreateHC)，並從這裡開始。

1. 在[Azure 入口網站](https://portal.azure.com)的左上角，按一下 [**新增** > **Web + 行動** > **Web 應用程式**。
    
    ![新的 web 應用程式][NewWebsite]
    
2. 在**Web 應用程式**防禦，以提供的 URL，然後按一下 [**建立**]。 
    
    ![網站名稱][WebsiteCreationBlade]
    
3. 在幾分鐘之後, 會建立 web 應用程式，並 web 應用程式刀顯示。 刀是垂直捲動的儀表板，可讓您管理您的網站。
    
    ![執行的網站][WebSiteRunningBlade]
    
4. 若要確認 live 網站，您可以按一下以顯示預設頁面的 [**瀏覽**] 圖示。
    
    ![按一下 [瀏覽] 以查看您的 web 應用程式][Browse]
    
    ![預設網頁應用程式][DefaultWebSitePage]
    
接下來，您將建立的混合式連線和 BizTalk 服務 web 應用程式。

<a name="CreateHC"></a>
## <a name="create-a-hybrid-connection-and-a-biztalk-service"></a>建立混合式連線和 BizTalk 服務 ##

1. 在您的 web 應用程式刀按一下**所有設定** > **網路** > **設定混合式連線端點**。
    
    ![混合式連線][CreateHCHCIcon]
    
2. 在混合式連線刀中，按一下 [**新增**。
    
    <!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
    
3. **新增的混合式連線**刀隨即會開啟。  這是您第一份的混合式部署連線，因為預先選取**新的混合式連線**選項，，並**建立混合式連線**刀會開啟以供您。
    
    ![建立混合式部署的連線][TwinCreateHCBlades]
    
    在**建立混合式連線刀**中︰
    - [**名稱**] 中，提供連線的名稱。
    - **主機名稱**，輸入主控您的資源的內部部署電腦的名稱。
    - **連接埠**，輸入您的內部部署資源使用 (SQL Server 預設的執行個體 1433) 的連接埠號碼。
    - 按一下 [ **Biz 與服務**


4. **建立 BizTalk 服務**刀隨即會開啟。 輸入 BizTalk 服務的名稱，然後再按一下**[確定**]。
    
    ![建立 BizTalk 服務][CreateHCCreateBTS]
    
    **建立 BizTalk 服務**刀隨即關閉，且您會回到**建立混合式連線**刀。
    
5. 在建立混合式連線刀中，按一下**[確定]**。 
    
    ![按一下 [確定]][CreateBTScomplete]
    
6. 程序完成後，請在入口網站中的 [通知] 區域會通知您已順利建立連線。
    < ！---TODO

    在此步驟中的所有項目會失敗。 無法在事入口網站中建立 BizTalk 服務。 我切換到傳統的入口網站 （完整入口網站），並建立 BizTalk 服務，但似乎可讓您 instance-當您完成建立混合式連線步驟時，您會收到下列錯誤，建立混合式連線 RelecIoudHC 失敗。 命名空間 」 的 api 版本 2014年-06-01 Microsoft.BizTaIkServices 」 中找不到的資源類型。
    
    此錯誤指出找不到類型]，[未執行個體。
    ![成功通知][CreateHCSuccessNotification]
    -->
7. 在 web 應用程式的刀**混合式連線**圖示現在會顯示已建立的 1 的混合式連接。
    
    ![建立一個混合式連線][CreateHCOneConnectionCreated]
    
此時，您已完成的雲端混合式連線基礎結構的重要部分。 接下來，您將建立對應的內部部署一段。

<a name="InstallHCM"></a>
## <a name="install-the-on-premises-hybrid-connection-manager-to-complete-the-connection"></a>安裝完成連接內部部署混合式連線管理員 ##

1. 按一下 [web 應用程式的刀，**所有設定** > **網路** > **設定混合式連線端點**。 
    
    ![混合式連線圖示][HCIcon]
    
2. 在**混合式連線**刀，最近新增的端點的 [**狀態**] 欄會顯示**未連線**。 按一下要設定讓它的連線。
    
    ![未連線][NotConnected]
    
    混合式連線刀隨即會開啟。
    
    ![NotConnectedBlade][NotConnectedBlade]
    
3. 按一下 [刀，**接聽項設定**。
    
    ![按一下 [接聽程式設定][ClickListenerSetup]
    
4. **混合式連線內容**刀隨即會開啟。 **內部部署混合式連線管理員**] 下選擇 [**按一下這裡以安裝**]。
    
    ![按一下這裡以安裝][ClickToInstallHCM]
    
5. 在 [應用程式執行安全性警告] 對話方塊中，選擇 [**執行**] 以繼續。
    
    ![選擇 [執行] 以繼續進行][ApplicationRunWarning]
    
6.  在 [**使用者帳戶控制**] 對話方塊中，選擇 [**是**]。
    
    ![選擇 [是]][UAC]
    
7. 混合式連線管理員下載並安裝適用於您。 
    
    ![安裝][HCMInstalling]
    
8. 安裝完成後，請按一下 [**關閉**]。
    
    ![按一下 [關閉]][HCMInstallComplete]
    
    在**混合式連線**刀中，[**狀態**] 欄現在會顯示**已連線**。 
    
    ![連線的狀態][HCStatusConnected]

完成混合式連線基礎結構時，您可以建立會使用該參數的混合式應用程式。 

>[AZURE.NOTE]下列各節說明如何使用行動應用程式.NET 後端專案的混合式連線。

## <a name="configure-the-mobile-app-net-backend-project-to-connect-to-the-sql-server-database"></a>設定行動應用程式.NET 後端專案以連線至 SQL Server 資料庫

在應用程式服務行動應用程式.NET 後端專案會是只 ASP.NET web 應用程式與其他行動應用程式 SDK 安裝及初始化。 若要使用行動應用程式後端 web 應用程式，您必須[下載並初始化行動應用程式.NET 後端 SDK](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#install-sdk)。  

Mobile 應用程式，您也需要定義內部部署資料庫的連線字串，及修改使用此連線後端。 

1. 在 Visual Studio 方案總管] 中開啟您的行動應用程式.NET 後端的 Web.config 檔案、 找出**connectionStrings**節、 新增新的 SqlClient 項目，例如下列指向內部部署的 SQL Server 資料庫︰

        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

    請記住，若要取代`<**secure_password**>`字串*HybridConnectionLogin*您建立的密碼。

3. 按一下 [儲存 web.config Visual Studio 中的 [**儲存**]。

    > [AZURE.NOTE]在本機電腦上執行時，會使用這項連線設定。 Azure 在執行時，此設定會覆寫入口網站中定義的連線設定。

4. 展開 [**模型**] 資料夾，並開啟資料模型檔案，以*Context.cs*。

6. 修改值傳遞**DbContext**執行個體建構`OnPremisesDBConnection`基底**DbContext**建構函式，類似以下的程式碼片段︰

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

    服務將會使用新的連線至 SQL Server 資料庫。

## <a name="update-the-mobile-app-backend-to-use-the-on-premises-connection-string"></a>更新行動應用程式後端使用內部部署連線字串

接下來，您需要新增應用程式的設定這個新的連接字串，這樣就可以使用從 Azure。  

1. 回到[Azure 入口網站](https://portal.azure.com)在您的行動應用程式的 web 應用程式後端程式碼中，按一下 [**所有設定**]，然後**應用程式設定**。

3. 在**Web 應用程式設定**刀中，向下捲動**連接字串**，並新增名為新**SQL Server**連線字串`OnPremisesDBConnection`與值要像`Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>`。

    取代`<**secure_password**>`與您內部部署的資料庫的安全密碼。

    ![內部部署資料庫的連線字串](./media/web-sites-hybrid-connection-get-started/set-sql-server-database-connection.png)

2. 按下 [**儲存**] 儲存混合式連線和您剛剛建立的連線字串。

此時，您可以重新發佈伺服器專案，並與您現有的行動應用程式用戶端測試新連線。 從讀取和寫入使用混合式連線的內部部署資料庫資料。

<a name="NextSteps"></a>
## <a name="next-steps"></a>後續步驟 ##

- 建立使用混合式連線的 ASP.NET web 應用程式的資訊，請參閱[連線至內部部署的 SQL Server Azure 網站的使用混合式連線](http://go.microsoft.com/fwlink/?LinkID=397979)。 

### <a name="additional-resources"></a>其他資源

[混合式連線概觀](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[處理器變化介紹混合式連線 （頻道 9 視訊）](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[混合式連線的網站](https://azure.microsoft.com/services/biztalk-services/)

[Biztalk︰ 儀表板、 監控、 小數位數、 設定和混合式連線] 索引標籤](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)

[建置順暢的應用程式可攜 (視訊頻道 9) 實際的混合式雲端](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[從 Azure 行動服務使用混合式連線 （頻道 9 視訊） 連線到內部部署的 SQL Server](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## <a name="whats-changed"></a>變更的項目
* 若要變更的指南，從網站應用程式服務請參閱︰ [Azure 應用程式服務與程式影響現有 Azure 服務](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
 