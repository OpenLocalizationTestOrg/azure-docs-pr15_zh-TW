<properties     
    pageTitle="Azure 資料工廠-範例" 
    description="Azure 資料工廠服務提供詳細範例。" 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---samples"></a>Azure 資料工廠-範例

## <a name="samples-on-github"></a>在 GitHub 的範例
[GitHub Azure DataFactory 存放庫](https://github.com/azure/azure-datafactory)包含數個範例，可協助您快速 Azure 資料工廠服務設定遞增 （或） 修改指令碼，並將其用於自己的應用程式。 Samples\JSON 資料夾包含 JSON 程式碼片段的常見的案例。

| 範例 | 描述 |
| :----- | :---------- | 
| [ADF 逐步解說](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFWalkthrough) | 此範例會提供用於處理使用 Azure 資料工廠開啟的記錄檔中的資料，獲得深入見解的記錄檔的端對端逐步解說。 <br/><br/>在此逐步解說，資料工廠管線收集範例記錄]，[處理程序豐富的參考資料記錄的資料，並轉換資料以評估最近尚未啟動的行銷活動的效益。 |
| [JSON 範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON) | 此範例提供常見的案例 JSON 範例。 | 
| [Http 資料下載的範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample) | 此範例 showcases 下載的 HTTP 端點 Azure Blob 儲存體使用自訂的.NET 活動的資料。 |
| [跨 AppDomain 點網路活動範例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) | 此範例可讓您撰寫自訂的.NET 活動的並不一定要使用的 ADF 啟動器 （例如，WindowsAzure.Storage v4.3.0、 Newtonsoft.Json v6.0.x 等） 的組件版本。 |
| [執行 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample) |  此範例會包含可用來叫用 RScript.exe 資料工廠自訂活動。 此範例只適用於已 R 安裝在您自己 （不視） HDInsight 叢集。 |
| [呼叫火花 HDInsight Hadoop 叢集上的工作](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) | 此範例會示範如何使用 MapReduce 活動叫用火花程式。 火花程式只將資料從一個 Azure Blob 容器到另一個。 |
| [使用 Azure 學習批次計分活動的 twitter 分析](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-AzureMLBatchScoringActivity) | 此範例會示範如何使用 AzureMLBatchScoringActivity 叫用執行 twitter 舉動分析，計分預測等的 Azure 電腦學習模型。 |
| [使用自訂活動的 twitter 分析](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |  此範例會示範如何使用自訂的.NET 活動叫用執行 twitter 舉動分析，計分預測等的 Azure 電腦學習模型。 |
| [Azure 機器學習的參數化的管線](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ParameterizedPipelinesForAzureML/) | 範例提供端對端 C# 程式碼部署 N 管線計分和重新訓練各有不同區域參數地區清單來自 parameters.txt 檔案，也就是包含在此範例的位置。 | 
| [參考資料重新整理 Azure 資料流分析工作](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs) |  此範例會示範如何 Azure 資料工廠和 Azure 資料流分析共同用來執行查詢，使用 [參考資料以及設定排程的參考資料重新整理。 |
| [使用內部部署 Hortonworks Hadoop 的混合式管線](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HybridPipelineWithOnPremisesHortonworksHadoop) | 此範例會使用內部部署 Hadoop 叢集做為計算目標資料工廠中執行的工作，就像等 HDInsight 基礎 Hadoop 叢集雲端，您可以新增其他計算目標。 |
| [JSON 轉換工具](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSONConversionTool) | 這項工具可讓您將 JSONs 轉換從 2015年-07-01-預覽最新] 或 [2015年-07-01-預覽 （預設） 之前的版本。 |  
| [U SQL 範例輸入的檔案](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/U-SQL%20Sample%20Input%20File) |  此檔案是 U SQL 活動所使用的範例檔案。 | 

## <a name="azure-resource-manager-templates"></a>Azure 資源管理員範本
您可以找到資料工廠上 Github 下列 Azure 資源管理員範本。 

| 範本 | 描述 |
| -------- | ----------- | 
| [從 Azure Blob 儲存體複製到 Azure SQL 資料庫](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy) | 將資料從指定的 Azure blob 儲存體到 Azure SQL 資料庫的管線部署此範本建立 Azure 資料工廠 |    
| [Salesforce 複製到 Azure Blob 儲存體](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy) | 將資料從指定的 Salesforce 帳戶到 Azure blob 儲存體的管線部署此範本建立 Azure 資料工廠。 |    
| [透過 Azure HDInsight 叢集上執行登錄區指令碼轉換的資料](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation) | 部署此範本建立 Azure 資料工廠管線 Azure HDInsight Hadoop 叢集上執行的範例登錄區指令碼轉換資料。 | 


## <a name="samples-in-azure-portal"></a>Azure 入口網站中的範例
您可以使用您的資料工廠首頁上的**範例管線**磚部署您資料的工廠範例管線與他們相關聯的實體 （資料集與連結的服務） 中。 

1. 建立資料工廠或開啟現有的資料工廠。 請參閱[快速入門 Azure 資料工廠](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#CreateDataFactory)的步驟來建立資料工廠。
2. 在資料工廠的**資料工廠**刀，按一下 [**範例管線**磚。

    ![管線方塊範例](./media/data-factory-samples/SamplePipelinesTile.png)

2. 在**範例管線**刀中，按一下您想要部署**範例**。 
    
    ![範例管線刀](./media/data-factory-samples/SampleTile.png)

3. 指定設定的設定，此範例。 例如您 Azure 儲存體帳戶名稱和帳戶金鑰、 Azure SQL 伺服器名稱、 資料庫、 使用者識別碼和密碼等。 

    ![範例刀](./media/data-factory-samples/SampleBlade.png)

4. 完成指定組態設定之後，請按一下 [**建立**]，建立/部署範例管線和連結的服務/資料表使用管線。
5. 部署的狀態會出現在您按一下較舊版本的**範例管線**刀方塊範例。

    ![部署狀態](./media/data-factory-samples/DeploymentStatus.png)

6. 當您看到範例的圖磚上的**部署成功**的訊息時，請關閉**範例管線**刀。  
5. 在**資料工廠**刀，您會看到連結的服務與資料集管線會新增至您的資料工廠。  

    ![資料工廠刀](./media/data-factory-samples/DataFactoryBladeAfter.png)
   
## <a name="samples-in-visual-studio"></a>在 Visual Studio 中的範例

### <a name="prerequisites"></a>必要條件

您必須在電腦上安裝下列動作︰ 

- Visual Studio 2013 或 Visual Studio 2015
- 下載 Azure SDK Visual Studio 2013 或 Visual Studio 2015。 瀏覽至[Azure 下載頁面](https://azure.microsoft.com/downloads/)，然後按一下**與 2013年**或**與 2015年** **.NET** ] 區段中。
- 下載最新的 Azure 資料工廠外掛程式的 Visual Studio︰[與 2013年](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5)或[與 2015年](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005)。 如果您使用 Visual Studio 2013，您也可以更新的外掛程式，請執行下列步驟︰ 在功能表中，按一下 [**工具** -> **Extensions 和更新** -> **Online** -> **Visual Studio 庫** -> **Microsoft Azure 資料工廠工具 Visual Studio** -> **更新**。

### <a name="use-data-factory-templates"></a>使用資料工廠範本

1. 按一下 [**檔案**] 功能表、 指向 [**新增**]，及按一下 [**專案**]。 
2. 在 [**新專案**] 對話方塊中，執行下列步驟︰ 
    1. 選取**範本**] 底下的**DataFactory** 。 
    2. 在右窗格中選取**資料工廠範本**。 
    3. 輸入專案**名稱**。 
    4. 選取專案的**位置**。 
    5. 按一下**[確定]**。 

    ![新增專案] 對話方塊](./media/data-factory-samples/vs-new-project-adf-templates.png)
6. 在**資料工廠範本**] 對話方塊中，選取 [**使用案例的範本**] 區段中，從 [範例範本，按一下 [**下一步**。 下列步驟會引導您完成使用**客戶設定檔**的範本。 步驟會類似的其他範例。 

    ![資料工廠範本] 對話方塊](./media/data-factory-samples/vs-data-factory-templates-dialog.png) 
7. 在 [**資料原廠設定**] 對話方塊中，按一下**下一個**頁面上的**資料工廠基本概念**。
8. **設定資料工廠**頁面上，執行下列步驟︰ 
    1. 選取 [**建立新的資料工廠**。 您也可以選取 [**使用現有的資料工廠**。
    2. 輸入資料 factory**名稱**。
    3. 選取您想要建立的資料工廠**Azure 訂閱**]。 
    4. 選取 [資料 factory**資源群組**]。
    5. 選取**西美國**、**東亞美國**或**北美歐洲****地區**。
    6. 按一下 [**下一步**]。 
9. **設定資料會儲存**在頁面中，指定現有**Azure SQL 資料庫**及**Azure 儲存體帳戶**（或） 建立資料庫/儲存空間，並按一下 [下一步]。 
10. 在**計算設定**] 頁面上，選取 [預設值]，然後按一下 [**下一步**。 
11. 在 [**摘要**] 頁面檢視所有設定，然後按一下 [**下一步**。 
12. 在 [**部署狀態**] 頁面中，等到完成部署時，然後按一下 [**完成]**。
13. 在方案總管中，以滑鼠右鍵按一下，然後按一下 [**發佈**]。 
19. 如果您看到**您的 Microsoft 帳戶登入**] 對話方塊中，輸入您的認證有 Azure 訂閱的帳戶，然後按一下 [**登入**。
20. 您應該會看到下列對話方塊中︰

    ![發佈] 對話方塊](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)

21. 在 [**設定資料工廠**] 頁面中，執行下列步驟︰ 
    1. 請確認該**使用現有的資料工廠**選項。
    2. 選取**資料工廠**您已選取時使用的範本。 
    6. 按一下 [**下一步**切換至**發佈的項目**頁面。 （按**] 索引標籤**移出 [名稱] 欄位，如果已停用 [**下一步**] 按鈕）。 
23. 在 [**發佈的項目**] 頁面中，確保所有資料工廠實體已選取，並按 [**下一步**切換至 [**摘要**] 頁面。     
24. 檢閱摘要，請按 [**下一步**開始部署程序，並檢視**部署狀態**。
25. 在 [**部署狀態**] 頁面中，您應該會看到部署程序的狀態。 部署完成之後，請按一下 [完成]。 

如需使用 Visual Studio 作者資料工廠實體，並將它們發佈到 Azure 詳細資訊，請參閱[建立第一個資料工廠 (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md) 。          