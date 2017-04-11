<properties
pageTitle="使用來自內部部署的 SQL Server 資料庫的資料中電腦學習 |Azure"
description="您可以使用來自內部部署的 SQL Server 資料庫的資料來執行 Azure 電腦學習使用進階的分析。"
services="machine-learning"
documentationCenter=""
authors="garyericson"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="machine-learning"
ms.workload="data-services"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="09/16/2016"
ms.author="garye;krishnan"/>

# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Azure 電腦學習使用從內部部署的 SQL Server 資料庫的資料與執行進階的分析


經常使用的內部部署資料的企業想要採取善用刻度和其電腦學習負載雲端的靈活性。 但又不中斷其目前的商務程序與工作流程，將其內部部署資料移至雲端。 Azure 電腦學習現在支援從內部部署的 SQL Server 資料庫讀取您的資料，然後訓練和計分此資料模型。 您不再需要手動複製並同步處理雲端與您的內部部署伺服器之間的資料。 相反地，**匯入資料**模組 Azure 電腦學習 Studio 中的可以立即閱讀直接從您內部部署的 SQL Server 資料庫訓練和計分工作。 

本文將概略瞭解如何輸入內部部署的 SQL server 資料到 Azure 電腦學習。 假設您已熟悉了 Azure 電腦學習概念，像是*工作區、 模組、 資料集、 實驗等*。

> [AZURE.NOTE] 此功能不適用於免費的工作區。 如需有關電腦學習定價和層級的詳細資訊，請參閱[Azure 電腦學習價格](https://azure.microsoft.com/pricing/details/machine-learning/)。

<!-- --> 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## <a name="install-the-microsoft-data-management-gateway"></a>安裝 Microsoft 資料管理閘道

若要存取內部部署的 SQL Server 資料庫中 Azure 電腦學習必須下載並安裝 Microsoft 資料管理閘道器。 當您將閘道器的連線設定電腦學習 Studio 中會有機會下載並安裝使用如下所述的 [**下載和註冊資料閘道器**] 對話方塊的閘道器。

您也可以下載並執行 MSI 安裝程式套件的從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=39717)安裝資料管理閘道提前。 選擇 [最新版本，選取 [32 位元或 64 位元，視您的電腦]。 MSI 可以用於升級為最新版本，現有資料管理閘道以保留所有設定。

閘道器具有下列先決條件︰

- 支援的 Windows 作業系統版本是 Windows 7、 Windows 8/8.1、 Windows 10、 Windows Server 2008 R2、 Windows Server 2012 和 Windows Server 2012 R2。
- 閘道器電腦的建議的設定不至少 2 GHz、 4 核心、 8 GB 的 RAM 及 80 GB 磁碟。
- 如果主機機器休眠，閘道器無法回應資料的要求。 閘道器在安裝之前，因此，在電腦上設定適當的電源計劃。 如果電腦設定為休眠閘道器安裝會顯示訊息。
- 複製活動時所發生的特定的頻率，因為資源使用狀況 （CPU、 記憶體） 在電腦上的也會依照相同的模式，最大使用量和空閒時間。 資源使用量也，取決於要移動的資料量。 進行中複製的多個工作時您會看到向上最大使用量的時段的資源使用狀況。 技術上而言足夠的最小的設定，列於上方時，您可能會想要有更多資源比根據特定負載移動資料的最小組態設定。

您應該考慮下列設定與使用資料管理閘道器時︰

- 您可以在一部電腦上安裝資料管理閘道器只有一個執行個體。
- 您可以使用單一閘道器的多個內部部署資料來源。
- 您可以在相同的內部部署資料來源連線不同的電腦上的多個閘道。
- 您一次設定只有一個工作區的閘道器。 閘道器無法在工作區共用這一次。
- 您可以設定單一工作區的多個的閘道。 例如，您可能要使用的閘道器之開發期間您測試資料來源連線及生產閘道，當您準備好 operationalize。
- 閘道器不需要在同一部電腦，做為資料來源，但保持深入瞭解資料來源減少閘道器至連線至資料來源的時間。 我們建議您在所裝載的內部部署資料來源，以便閘道器與資料來源不競爭資源的不同的電腦上安裝閘道器。
- 如果您已經做 Power BI 或 Azure 資料工廠案例您電腦上安裝的閘道器，請安裝 Azure 電腦學習的另一個閘道器在另一部電腦上。 

    > [AZURE.NOTE] 您無法在同一部電腦上執行資料管理閘道器和 Power BI 閘道。

- 您需要的 Azure 電腦學習使用資料管理閘道，即使您使用 Azure ExpressRoute 其他資料。 您必須將您的資料來源為內部部署資料來源 （亦即在防火牆後） 即使當您使用 ExpressRoute，並使用資料管理閘道器建立電腦學習與資料來源之間的連線。 

您可以找到安裝先決條件、 安裝步驟及疑難排解提示的詳細的資訊，請參閱，[移動內部部署與雲端資料管理閘道器之間的資料](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway)，請開頭] 區段中，[使用資料管理閘道器的考量](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway)。

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>從您內部部署的 SQL Server 資料庫至 Azure 電腦學習輸入資料


在此逐步解說，您會設定 Azure 電腦學習工作區中的資料管理閘道、 設定，然後讀取從內部部署的 SQL Server 資料庫的資料。

> [AZURE.TIP] 在開始之前，停用瀏覽器的快顯封鎖程式`studio.azureml.net`。 如果您使用的 Google Chrome 瀏覽器，請下載並安裝的其中一個多個外掛程式集在 Google Chrome 網站儲存[按一次應用程式副檔名](https://chrome.google.com/webstore/search/clickonce?_category=extensions)。

### <a name="step-1-create-a-gateway"></a>步驟 1︰ 建立閘道器

第一步是建立及設定閘道器來存取您內部部署的 SQL 資料庫。

1. 登入[Azure 電腦學習 Studio](https://studio.azureml.net/Home/)並選取您想要使用工作區。

2. 按一下 [**設定**刀左側，然後按一下 [頂端的 [**資料閘道器**] 索引標籤。

3. 按一下畫面底部的**新資料閘道器**。

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)

4. 在 [**新資料閘道器**] 對話方塊中，輸入**閘道器名稱**，您也可以新增**描述**。 按一下 [上移至下一個步驟，設定的右下角的箭號。

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)

5. 下載和註冊資料閘道器] 對話方塊中，複製到剪貼簿的閘道器登錄機碼。

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)

6. <span id="note-1" class="anchor"></span>如果您還沒有下載並安裝 Microsoft 資料管理閘道器，然後按一下 [**下載資料管理閘道器**]。 這會帶您到 Microsoft 下載中心您可以在其中選取的閘道版本，需要請下載，並將其安裝。 您可以[將資料內部部署與雲端資料管理閘道器之間移動](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)文件的開頭各節中找到安裝先決條件、 安裝步驟及疑難排解提示的詳細的資訊。

7. 閘道器安裝後，資料管理閘道器組態管理員將開啟，並**註冊閘道器**] 對話方塊隨即出現。 貼上您複製到剪貼簿**閘道登錄機碼**，然後按一下 [**註冊**]。

8. 如果您已經安裝的閘道器，請執行資料管理閘道器組態管理員、 按一下 [**變更索引鍵**，貼上您複製到剪貼簿， **閘道登錄機碼**，按一下**[確定]**。

9. 安裝完成後，會顯示 [**註冊閘道器**] 對話方塊的 Microsoft 資料管理閘道器組態管理員。 貼上您複製到剪貼簿上述閘道器登錄機碼，然後按一下 [**註冊**]。

    ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)

10. 在 [**常用**] 索引標籤 Microsoft 資料管理閘道器組態管理員中設為下列的值時，完成閘道器設定︰

    - **閘道器名稱**，以及**執行個體名稱**會設定為 [閘道器的名稱。

    - **登錄**設定為 [**已註冊**]。

    - **狀態**設定為 [**已啟動**。

    - 底部的狀態列會顯示**已連線至資料管理閘道器雲端服務**，以及綠色的核取記號。

     ![](media/machine-learning-use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

     Azure 電腦學習 Studio 也會更新成功註冊時。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-registered.png)

11. 在 [**下載和註冊資料閘道器**] 對話方塊中，按一下 [核取記號，以完成安裝]。 [**設定**] 頁面會顯示閘道器狀態為 「 線上 」。 在右側窗格中，您可以找到狀態及其他實用的資訊。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\gateway-status.png)

12. 在 Microsoft 資料管理閘道器組態管理員切換至 [**憑證**] 索引標籤。 指定此索引標籤上的憑證用於加密/解密內部部署資料存放區中的入口網站所指定的認證。 這是產生的預設憑證。 Microsoft 建議改成您自己的憑證管理系統的備份的憑證。 按一下 [**變更**，請改為使用您自己的憑證。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-certificate.png)

13. （選用）如果您想要啟用才能疑難排解問題閘道器的詳細資訊記錄時，Microsoft 資料管理閘道器組態管理員中切換至 [**診斷**] 索引標籤，然後核取 [**啟用疑難排解的詳細資訊記錄**] 選項。 可以在**應用程式及服務記錄檔**的 Windows 事件檢視器中找到的記錄資訊 -&gt; **資料管理閘道器**節點。 您也可以使用 [**診斷**] 索引標籤，測試使用閘道器的內部部署資料來源連線。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\data-gateway-configuration-manager-verbose-logging.png)

完成閘道器設定中 Azure 電腦學習程序。
現在，您準備好要使用您的內部部署資料。

您可以建立及設定 Studio 中每個工作區的多個閘道。 例如，您可能需要的閘道器之您想要在開發期間，您測試資料來源連線及不同閘道生產資料來源的。 Azure 電腦學習可讓您設定您的公司環境而定的多個閘道器的彈性。 目前您無法共用閘道之間的工作區，只有一個閘道器可以在一部電腦上安裝。 更多安裝閘道器時的考量，請參閱在文章中，[移動內部部署與雲端資料管理閘道器之間的資料](../data-factory/data-factory-move-data-between-onprem-and-cloud.md)[使用資料管理閘道器的考量](../data-factory/data-factory-move-data-between-onprem-and-cloud.md#considerations-for-using-data-management-gateway)。

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>步驟 2︰ 使用閘道器讀取從內部部署資料來源的資料

您將閘道器設定之後，您可以新增**資料匯入**模組至實驗輸入內部部署的 SQL Server 資料庫中的資料。

1.  在電腦學習 Studio 中，選取**實驗**] 索引標籤、 按一下 [ **+ 新增**在左下角，並選取**空白實驗**（或選取其中一個可用的幾個範例實驗）。

2.  尋找並將**資料匯入**模組拖曳到實驗畫布。

3.  按一下 [畫布之下的 [**另存新檔**]。 輸入 「 Azure 電腦學習內部部署 SQL Server 教學課程 「 實驗名稱，選取工作區中，然後按一下**[確定]**核取記號。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\experiment-save-as.png)

4.  按一下 [**匯入資料**模組加以選取，然後畫布右側的 [**屬性**] 窗格中，請在 [**資料來源**] 下拉式清單中選取 「 內部部署的 SQL 資料庫 」。

5.  選取 [**資料閘道**您安裝並註冊]。 您可以設定另一個閘道器，選取 [（新增新資料閘道...）]。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-select-on-premises-data-source.png)

6.  輸入 SQL**資料庫伺服器的名稱**和**資料庫名稱**，以及您要執行 SQL**資料庫查詢**。

7.  按一下 [**使用者名稱和密碼**] 底下的 [**輸入值**，然後輸入您的資料庫認證。 您可以使用 Windows 整合式驗證或 SQL Server 驗證您的內部部署的 SQL Server 的設定方式而定。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\database-credentials.png)
    
    郵件所需的 [值] 會變更 「 值設定 「 綠色核取記號。 您只需要輸入認證，除非您的資料庫資訊或密碼變更。 Azure 電腦學習使用您安裝來加密在雲端認證的閘道器時所提供的憑證。 Azure 永遠不會儲存不加密的內部部署認證。

    ![](media\machine-learning-use-data-from-an-on-premises-sql-server\import-data-properties-entered.png)

8.  按一下 [執行實驗的 [**執行**]。

實驗完成後執行您能以視覺化方式呈現您匯入資料庫，按一下 [**匯入資料**] 模組的輸出連接埠]，選取**視覺效果**的資料。

當您完成開發實驗時，您可以部署及 operationalize 模型。 使用批次執行服務，從內部部署的 SQL Server 資料庫**匯入資料]**模組中設定的資料會閱讀並用於分數。 雖然您可以使用要求回應服務計分內部部署資料，Microsoft 就會建議改為使用[Excel 增益集](machine-learning-excel-add-in-for-web-services.md)。 目前，撰寫為內部部署的 SQL server 資料庫**匯出資料**至不支援您實驗或發佈的 web 服務。

