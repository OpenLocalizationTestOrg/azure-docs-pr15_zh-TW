<properties 
   pageTitle="瞭解資料湖分析及 U SQL 使用互動式 Azure 入口網站的教學課程 |Azure" 
   description="快速開始學習資料湖分析及 U SQL。 " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="use-azure-data-lake-analytics-interactive-tutorials"></a>使用 Azure 資料湖分析互動式教學課程

Azure 入口網站提供互動式教學課程中的，以開始使用資料湖分析。 此文件會顯示您如何進行教學課程中的分析網站記錄檔。


>[AZURE.NOTE]如果您想要透過使用 Visual Studio 相同的教學課程，請參閱[使用資料湖分析分析網站記錄](data-lake-analytics-analyze-weblogs.md)。
>更多互動式教學課程都會新增至入口網站。


其他教學課程，請參閱︰

- [使用 Azure 入口網站的資料湖分析快速入門](data-lake-analytics-get-started-portal.md)
- [使用 PowerShell 的 Azure 資料湖分析快速入門](data-lake-analytics-get-started-powershell.md)
- [使用.NET SDK 資料湖分析快速入門](data-lake-analytics-get-started-net-sdk.md)
- [開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具](data-lake-analytics-data-lake-tools-get-started.md) 

**必要條件**

本教學課程之前，您必須具備下列項目︰

- **資料湖分析帳戶**。  請參閱[開始使用 Azure 資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md)。

##<a name="create-data-lake-analytics-account"></a>建立資料湖分析帳戶 

您必須有資料湖分析帳戶，才能執行任何工作。

每個資料湖分析帳戶有[Azure 資料湖存放](../data-lake-store/data-lake-store-overview.md)帳戶相依性。  此帳戶被指為預設資料湖存放帳戶。  事先或建立您的資料湖分析帳戶時，您可以建立的資料湖存放帳戶。 在本教學課程中，您將建立的資料湖存放帳戶使用狀況分析帳戶

**若要建立資料湖分析帳戶**

1. [Azure 入口網站](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute)登入。
2. 按一下 [ **Microsoft Azure**中開啟 StartBoard 左上角。
3. 按一下 [**市集**] 磚。  
3. 在**所有項目**刀，然後按**ENTER**上的 [搜尋] 方塊中輸入**Azure 資料湖分析**。 您應該在清單中，請參閱**Azure 資料湖分析**。
4. 從清單中按一下**Azure 資料湖分析**。
5. 按一下 [**建立**刀底部。
6. 輸入或選取下列選項︰

    ![Azure 資料湖分析入口網站刀](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

    - **名稱**︰ 分析帳戶的名稱。
    - **資料湖存放**︰ 每個資料湖分析帳戶擁有相依資料湖存放帳戶。 資料湖分析帳戶和相依資料湖存放帳戶必須位於相同的 Azure 資料中心。 請依照下列指示建立新的資料湖存放帳戶，或選取現有的項目。
    - **訂閱**︰ 選擇 [使用狀況分析帳戶 Azure 訂閱。
    - **資源群組**]。 選取現有的 Azure 資源群組或建立新的項目。 應用程式通常是許多元件，例如在 web 應用程式、 資料庫、 資料庫伺服器、 儲存及第 3 廠商服務。 Azure 資源管理員 (ARM) 可讓您使用應用程式] 群組中，稱為 Azure 資源] 群組中的資源。 您可以部署、 更新、 監控或刪除的所有資源在單一、 協同作業應用程式。 使用範本，以供部署，該範本能夠為不同的環境，例如執行與生產測試。 您可以檢視上顯型成本給整個群組，計費釐清為您的組織。 如需詳細資訊，請參閱[Azure 資源管理員的概觀](azure-resource-manager/resource-group-overview.md)。 
    - **位置**。 選取資料湖分析帳戶 Azure 資料中心。 
7. 選取 [**固定至 Startboard**。 這是需遵循此教學課程。
8. 按一下 [**建立**]。 它會帶您到 StartBoard 入口網站。 新方塊會新增至 [首頁] 頁面，顯示 「 部署 Azure 資料湖分析] 標籤。 花一些時間才建立的資料湖分析帳戶。 建立帳戶時，入口網站隨即會開啟新的刀上的帳戶。

    ![Azure 資料湖分析入口網站刀](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##<a name="run-website-log-analysis-interactive-tutorial"></a>執行網站記錄分析互動式教學課程

**若要開啟網站記錄分析互動式教學課程**

1. 從網站中，按一下 [ **Microsoft Azure**從左側功能表開啟 StartBoard。
2. 按一下連結至您的資料湖分析帳戶的磚。
3. 按一下 [**基本資訊**列中的 [**瀏覽互動式教學課程**]。

    ![資料湖分析互動式教學課程](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. 如果您看到橘色的警告說 「 範例未設定，按一下 [...]，按一下 [**複製範例資料**的範例資料複製到預設資料湖存放帳戶。 互動式教學課程必須執行的資料。
5. 從**互動式教學課程**刀中，按一下 [**網站記錄分析**]。 入口網站開啟新的入口網站刀教學課程。
5. 按一下 [ **1 簡介**，然後依照指示

##<a name="see-also"></a>另請參閱

- [Microsoft Azure 資料湖分析的概觀](data-lake-analytics-overview.md)
- [開始使用資料湖分析使用 Azure 入口網站](data-lake-analytics-get-started-portal.md)
- [使用 PowerShell 的 Azure 資料湖分析快速入門](data-lake-analytics-get-started-powershell.md)
- [開發 U SQL 指令碼的 Visual Studio 中使用資料湖工具](data-lake-analytics-data-lake-tools-get-started.md)
- [分析網站記錄使用 Azure 資料湖狀況分析](data-lake-analytics-analyze-weblogs.md)
