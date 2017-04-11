<properties
    pageTitle="不按比例縮放設定 Azure 中的應用程式 |Microsoft Azure"
    description="瞭解如何能夠拓展新增容量及功能 Azure 應用程式服務中的應用程式。"
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
    ms.date="07/05/2016"
    ms.author="cephalin"/>

# <a name="scale-up-an-app-in-azure"></a>不按比例縮放設定 Azure 中的應用程式 #

本文將示範如何 Azure 應用程式服務中調整您的應用程式。 有兩個工作流程的縮放比例、 縮放比例設定，時幅] 和 [本文說明 [縮放比例設定工作流程。

- [不按比例縮放](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)︰ 取得更多的 CPU、 記憶體、 磁碟空間及專用的虛擬機器 (Vm)、 自訂網域等憑證，暫存之位置、 自動縮放，以及其他額外的功能。 藉由變更您的應用程式所屬的應用程式服務方案的價格層縮放。
- [延展](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)︰ 增加 VM 執行您的應用程式的執行個體數目。
您可以調整查看為 20 執行個體，根據您的價格層。 **進階版**層的[應用程式服務環境](../app-service/app-service-app-service-environments-readme.md)進一步會增加擴充統計 50 的執行個體。 如需有關擴展的詳細資訊，請參閱[手動] 或 [自動調整執行個體計數](../monitoring-and-diagnostics/insights-how-to-scale.md)。 那里您將瞭解如何使用自動縮放，即不按比例縮放自動根據預先定義的規則與排程的執行個體計數。

縮放設定會套用，並會影響您的[應用程式服務方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)中的所有應用程式的秒數。
他們不需要您要變更您的程式碼或重新部署您的應用程式。

定價和個別的應用程式服務方案的功能的相關資訊，請參閱[應用程式服務價格詳細資料](/pricing/details/web-sites/)。  

> [AZURE.NOTE] 切換從**Free**層的應用程式服務方案之前，您必須先移除[花費限制](/pricing/spending-limits/)就地 Azure 訂閱。 若要檢視或變更您的 Microsoft Azure 應用程式服務訂閱的選項，請參閱[Microsoft Azure 訂閱][azuresubscriptions]。

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>不按比例縮放設定您的價格層

1. 在瀏覽器中開啟 [ [Azure 入口網站][portal]。

2. 在您的應用程式刀中按一下 [**所有設定**]，然後按一下**縮放比例設定**。

    ![瀏覽至不按比例縮放設定 Azure 應用程式。][ChooseWHP]

4. 選擇您層]，然後再按一下 [**選取**]。

    作業完成後，[**通知**] 索引標籤會 flash 綠色**成功**。

<a name="ScalingSQLServer"></a>
## <a name="scale-related-resources"></a>不按比例縮放相關的資源
如果您的應用程式取決於其他服務，例如 Azure SQL 資料庫或 Azure 儲存空間，您也可以調整這些資源，根據您的需求。 這些資源不會縮放與應用程式服務方案，以及必須分別縮放。

1. 在 [**基本資訊**，按一下 [**資源群組**] 連結。

    ![不按比例縮放設定 Azure 應用程式的相關資源](./media/web-sites-scale/RGEssentialsLink.png)

2. **資源群組**刀**摘要**組件，按一下您想要不按比例縮放的資源。 下圖顯示 [SQL 資料庫資源及 Azure 儲存體資源。

    ![瀏覽至 [資源群組刀能夠拓展您 Azure 應用程式](./media/web-sites-scale/ResourceGroup.png)

3. SQL 資料庫資源，按一下 [**設定** > **價格層**縮放價格層。

    ![不按比例縮放設定 SQL 資料庫後端 Azure 應用程式](./media/web-sites-scale/ScaleDatabase.png)

    您也可以開啟[地理複寫](../sql-database/sql-database-geo-replication-overview.md)，SQL 資料庫執行個體。

    Azure 儲存體資源，按一下 [**設定** > **設定**能夠拓展您儲存空間的選項。

    ![不按比例縮放設定 Azure 應用程式所使用的 Azure 儲存體帳戶](./media/web-sites-scale/ScaleStorage.png)

<a name="devfeatures"></a>
## <a name="learn-about-developer-features"></a>深入瞭解開發人員功能
定價層中，根據開發人員導向的可用功能如下︰

### <a name="bitness"></a>位元 ###

- 將**基本**、**標準**、 和**進階版**層支援 64 位元與 32 位元的應用程式。
- **免費**，**共用**計劃層支援只 32 位元應用程式。

### <a name="debugger-support"></a>偵錯工具支援 ###

- 使用**免費**、**共用**和**基本**模式，在應用程式服務方案每一個連線偵錯工具支援。
- 使用 [**一般**] 及 [**進階**模式，在每個應用程式服務方案的五個同時連線的偵錯工具支援。

<a name="OtherFeatures"></a>
## <a name="learn-about-other-features"></a>深入瞭解其他功能

- 如需所有剩餘的應用程式服務方案，包括價格的功能和功能 （包括開發人員） 的所有使用者的感興趣的詳細資訊，請參閱[應用程式服務價格詳細資料](/pricing/details/web-sites/)。

>[AZURE.NOTE] 如果您想要快速入門 Azure 應用程式服務註冊 Azure 帳戶之前，請移至[嘗試應用程式服務](http://go.microsoft.com/fwlink/?LinkId=523751)可以讓您立即建立短暫入門 web 應用程式在應用程式服務。 沒有信用卡必且沒有承諾。

<a name="Next Steps"></a>
## <a name="next-steps"></a>後續步驟

- 若要開始使用 Azure，請參閱[Microsoft Azure 免費試用版](/pricing/free-trial/)。
- 價格、 支援和 SLA 的相關資訊，請造訪下列連結。

    [資料傳輸的定價詳細資料](/pricing/details/data-transfers/)

    [Microsoft Azure 支援計劃](/support/plans/)

    [服務等級協定](/support/legal/sla/)

    [SQL 資料庫定價詳細資料](/pricing/details/sql-database/)

    [虛擬機器和 Microsoft Azure 雲端服務的大小][vmsizes]

    [應用程式服務價格的詳細資料](/pricing/details/app-service/)

    [定價詳細資料-SSL 連線的應用程式服務](/pricing/details/web-sites/#ssl-connections)

- Azure 應用程式服務的相關資訊的最佳方式，包括建立可調整和彈性的結構，請參閱[最佳作法︰ Azure 應用程式服務 Web 應用程式](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx)。

- 縮放比例應用程式服務應用程式的影片，請參閱下列資源︰

    - [Azure 網站-宗 Schackow 不按比例縮放的時機](/documentation/videos/azure-web-sites-free-vs-standard-scaling/)
    - [自動調整大小]、 [CPU Azure 網站或排程-宗 Schackow](/documentation/videos/auto-scaling-azure-web-sites/)
    - [如何 Azure 網站隨著-宗 Schackow](/documentation/videos/how-azure-web-sites-scale/)


<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ChooseBasicInstances]: ./media/web-sites-scale/scale2InstancesBasic.png
[SaveButton]: ./media/web-sites-scale/05SaveButton.png
[BasicComplete]: ./media/web-sites-scale/06BasicComplete.png
[ScaleStandard]: ./media/web-sites-scale/scale3InstancesStandard.png
[Autoscale]: ./media/web-sites-scale/scale4AutoScale.png
[SetTargetMetrics]: ./media/web-sites-scale/scale5AutoScaleTargetMetrics.png
[SetFirstRule]: ./media/web-sites-scale/scale6AutoScaleFirstRule.png
[SetSecondRule]: ./media/web-sites-scale/scale7AutoScaleSecondRule.png
[SetThirdRule]: ./media/web-sites-scale/scale8AutoScaleThirdRule.png
[SetRulesFinal]: ./media/web-sites-scale/scale9AutoScaleFinal.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
