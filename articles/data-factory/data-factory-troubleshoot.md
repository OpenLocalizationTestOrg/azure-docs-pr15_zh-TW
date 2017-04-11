<properties 
    pageTitle="Azure 資料工廠問題進行疑難排解" 
    description="瞭解如何使用 Azure 資料工廠問題進行疑難排解。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/31/2016" 
    ms.author="spelluru"/>

# <a name="troubleshoot-data-factory-issues"></a>資料工廠問題進行疑難排解
這份文件在使用 Azure 資料工廠時，會提供問題的疑難排解秘訣。 這份文件不會列出所有可能的問題時使用的服務，但它涵蓋了一些問題和一般疑難排解秘訣。   

## <a name="troubleshooting-tips"></a>疑難排解提示

### <a name="error-the-subscription-is-not-registered-to-use-namespace-microsoftdatafactory"></a>錯誤︰ 若要使用的命名空間 'Microsoft.DataFactory' 未註冊訂閱
如果您收到此錯誤，Azure 資料工廠資源提供者尚未註冊您的電腦上。 執行下列動作︰ 

1. 啟動 Azure PowerShell。 
2. 使用下列命令 Azure 帳戶登入。
        登入 AzureRmAccount 
3. 執行下列命令以註冊 Azure 資料工廠提供者。
        Register AzureRmResourceProvider-ProviderNamespace Microsoft.DataFactory

### <a name="problem-unauthorized-error-when-running-a-data-factory-cmdlet"></a>問題︰ 未經授權的錯誤時執行資料工廠 cmdlet
使用 PowerShell 的 Azure，您可能不使用右 Azure 帳戶或訂閱。 使用下列 cmdlet，以選取右 Azure 帳戶和 Azure 搭配使用的訂閱。 

1. 登入-AzureRmAccount-使用正確的使用者識別碼和密碼
2. 取得 AzureRmSubscription-檢視所有訂閱的帳戶。 
3. 選取 AzureRmSubscription&lt;訂閱名稱&gt;-選取正確的訂閱。 使用您用於 Azure 入口網站上建立的資料工廠相同。

### <a name="problem-fail-to-launch-data-management-gateway-express-setup-from-azure-portal"></a>問題︰ 無法啟動從 Azure 入口網站的資料管理閘道器 Express 安裝程式
資料管理閘道器的 [快速設定需要 Internet Explorer 或 Microsoft ClickOnce 相容的網頁瀏覽器。 如果 Express 安裝程式無法啟動，請執行下列其中一項︰ 

- 使用 Internet Explorer 或 Microsoft ClickOnce 相容的網頁瀏覽器。

    如果您使用 Chrome，請移至[Chrome web store](https://chrome.google.com/webstore/)，搜尋 「 ClickOnce 」 關鍵字，選擇其中一個 [ClickOnce 副檔名]，並將其安裝。 
    
    若是 Firefox （安裝增益集） 執行相同的結果。 按一下 [開啟功能表] 工具列上的按鈕 （在右上角的三個水平）、 按一下 [附加元件、 搜尋關鍵字 「 ClickOnce 」 相關、 選擇其中一個 ClickOnce 延伸模組，並將其安裝。 

- 使用相同的刀在入口網站上顯示的 [**手動設定**] 連結。 您可以使用此方法若要下載安裝檔案，並手動執行。 已成功安裝後，您會看到資料管理閘道器設定] 對話方塊。 複製**鍵**從入口網站的畫面並使用組態管理員中手動登錄閘道器的服務。  

### <a name="problem-fail-to-connect-to-on-premises-sql-server"></a>問題︰ 無法連線到內部部署的 SQL Server 
閘道器電腦上啟動**資料管理閘道器組態管理員**並使用 [**疑難排解**] 索引標籤，測試連線至 SQL Server，從閘道器的電腦。 如需疑難排解連線/閘道器的秘訣請參閱[疑難排解閘道器問題](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)相關問題。   
 

### <a name="problem-input-slices-are-in-waiting-state-for-ever"></a>問題︰ 為曾等候狀態位於輸入扇形區

扇形區可能原因，各種**等待**的狀態。 常見的原因之一是不 [**外部**] 屬性設為**true**。 **外部**屬性會標示所產生的 Azure 資料工廠範圍之外的任何資料集。 這個屬性會表示的資料是由資料 factory 內任何管線不備份及外部。 資料扇形區標示為 [**準備好**後個別的儲存區中的資料是使用。 

請參閱下列範例**外部**屬性的使用方式。 您可以選擇指定**externalData*** 當您設定外部為 true。

如需有關此屬性的詳細資訊，請參閱[資料集](data-factory-create-datasets.md)文章。
    
    {
      "name": "CustomerTable",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "MyLinkedService",
        "typeProperties": {
          "folderPath": "MyContainer/MySubFolder/",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": ";"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          }
        }
      }
    }

若要解決此錯誤，JSON 定義的輸入表格中新增**外部**屬性和 [選擇性**externalData** ] 區段中，重新建立表格。 

### <a name="problem-hybrid-copy-operation-fails"></a>問題︰ 混合式複製作業失敗
請參閱[疑難排解閘道器問題](data-factory-data-management-gateway.md#troubleshoot-gateway-issues)的步驟來疑難排解複製從內部部署資料存放區使用資料管理閘道的問題。 

### <a name="problem-on-demand-hdinsight-provisioning-fails"></a>問題︰ 視 HDInsight 佈建失敗
使用連結的類型 HDInsightOnDemand 的服務，當您需要指定 linkedServiceName 指向 Azure Blob 儲存體。 資料工廠 service 會使用此儲存儲存記錄檔和視 HDInsight 叢集支援的檔案。  有時視 HDInsight 叢集的佈建失敗，發生下列錯誤︰

        Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

此錯誤通常表示 linkedServiceName 中指定的儲存空間帳戶的位置不在相同的資料中心位置位置 HDInsight 佈建新鮮事。 範例︰ 如果您的資料工廠位於西美國 Azure 儲存是在 [美國東亞，視需要佈建失敗西美國。

此外，還有第二個 JSON 屬性 additionalLinkedServiceNames 位置額外儲存空間的帳戶可能會視 HDInsight 中指定。 這些額外連結的儲存空間帳戶應該位於 HDInsight 叢集，相同的位置，或相同的錯誤而失敗。

### <a name="problem-custom-net-activity-fails"></a>問題︰ 自訂.NET 活動失敗
如需詳細步驟，請參閱[偵錯含自訂活動的管線](data-factory-use-custom-activities.md#debug-the-pipeline)。 

## <a name="use-azure-portal-to-troubleshoot"></a>若要疑難排解使用 Azure 入口網站 

### <a name="using-portal-blades"></a>使用入口網站刀
請參閱步驟[監視器管道的郵件](data-factory-build-your-first-pipeline-using-editor.md#monitor-pipeline)。 

### <a name="using-monitor-and-manage-app"></a>使用 [監控和管理應用程式
請參閱[監控和管理資料工廠管線監控和管理應用程式使用](data-factory-monitor-manage-app.md)如需詳細資訊。 

## <a name="use-azure-powershell-to-troubleshoot"></a>使用 PowerShell 的 Azure 疑難排解

### <a name="use-azure-powershell-to-troubleshoot-an-error"></a>使用 PowerShell 的 Azure 錯誤的疑難排解  
如需詳細資訊，請參閱[使用 PowerShell 的 Azure 監視器資料工廠管線](data-factory-build-your-first-pipeline-using-powershell.md#monitor-pipeline)。 


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png
 