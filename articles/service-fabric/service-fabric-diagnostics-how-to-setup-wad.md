<properties
   pageTitle="使用 Azure 診斷收集記錄 |Microsoft Azure"
   description="本文將說明如何設定 Azure 診斷記錄收集服務布料的轉印圖樣叢集 Azure 中執行。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>使用 Azure 診斷收集記錄

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

當您執行的 Azure 服務布料的轉印圖樣叢集時，最好從中央位置中的所有節點收集記錄。 無法在一個中央位置的記錄可協助您分析和疑難排解問題叢集或應用程式與服務的叢集在執行中的問題。

上傳並收集記錄的其中一個方法是使用 Azure 診斷分機] 上, 傳至 Azure 儲存體的記錄。 記錄不是直接在存放區中也有幫助的。 但您可以使用外部的程序來讀取儲存空間，並將其置於產品，例如[記錄分析](../log-analytics/log-analytics-service-fabric.md)、[彈性的搜尋](service-fabric-diagnostic-how-to-use-elasticsearch.md)或另一種記錄剖析的解決方案。

## <a name="prerequisites"></a>必要條件
您會使用這些工具執行某些作業的文件中︰

* [Azure 診斷程式](../cloud-services/cloud-services-dotnet-diagnostics.md)（相關 Azure 雲端服務，但有寶貴資訊與範例）
* [Azure 資源管理員](../azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Azure 資源管理員用戶端](https://github.com/projectkudu/ARMClient)
* [Azure 資源管理員範本](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## <a name="log-sources-that-you-might-want-to-collect"></a>您可能想要收集的記錄來源
- **服務布料的轉印圖樣記錄**︰ 標準追蹤 Windows 事件 (ETW) 和 EventSource 頻道發出平台。 記錄可以是下列其中一種類型︰
  - 作業事件︰ 服務布料的轉印圖樣平台執行的作業的記錄。 範例包括建立應用程式與服務、 節點的狀態變更和升級資訊。
  - [程式設計模型事件可靠的動作項目](service-fabric-reliable-actors-diagnostics.md)
  - [可靠的服務程式設計模型事件](service-fabric-reliable-services-diagnostics.md)
- **應用程式的事件**︰ 發出從您的服務的程式碼，並使用 EventSource 協助程式類別中的 Visual Studio 範本提供寫出的事件。 如需有關如何從您的應用程式寫入記錄檔的詳細資訊，請參閱[監視器和診斷在本機電腦開發設定服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。


## <a name="deploy-the-diagnostics-extension"></a>部署診斷副檔名
收集記錄的第一個步驟是部署每個服務布料的轉印圖樣叢集 Vm 診斷副檔名。 診斷副檔名為每個 VM 用來收集相關記錄，並將它們上載至您指定的儲存空間帳戶。 步驟會稍有根據您使用的是 Azure 入口網站或 Azure 資源管理員而定。 步驟也會有所不同根據是否部署屬於叢集建立或叢集已經存在的項目。 讓我們來看看每個案例的步驟。

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>部署診斷副檔名叢集建立透過入口網站的一部分
若要部署屬於叢集建立叢集 Vm 診斷副檔名，您可以使用診斷程式設定] 面板下圖所示。 若要啟用可靠的動作項目或可靠的服務事件集合，請確定的診斷程式設定為**在**（預設設定）。 建立叢集之後，您無法變更這些設定以使用入口網站。

![在入口網站中的 [叢集建立 azure 診斷程式設定](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Azure 支援小組*要求*支援記錄以協助解決您建立的任何支援要求。 這些記錄收集即時，並且儲存在其中建立 [資源] 群組中的儲存空間帳戶。 診斷程式設定設定應用程式層級的事件。 這些事件包含[可靠的動作項目](service-fabric-reliable-actors-diagnostics.md)事件、[可靠的服務](service-fabric-reliable-services-diagnostics.md)事件，以及一些系統層級服務布料的轉印圖樣事件會儲存在 Azure 儲存體。

產品，例如[彈性的搜尋](service-fabric-diagnostic-how-to-use-elasticsearch.md)或您自己的程序可以取得事件從儲存的帳戶。 目前有辦法篩選或 groom 會傳送至資料表事件。 如果您沒有實作程序來移除表格中的事件，表格會繼續變大。

當您要使用入口網站建立叢集時，我們強烈建議您下載範本*，再按一下* *[確定]** * 若要建立叢集。 如需詳細資訊，請參閱[設定服務布料的轉印圖樣叢集使用 Azure 資源管理員範本](service-fabric-cluster-creation-via-arm.md)。 您需要的範本，以進行變更之後，因為您無法使用入口網站中進行變更。

您可以使用下列步驟，從入口網站匯出的範本。 不過，這些範本更多時，可能難以使用，因為他們可能會遺失必要的資訊的 null 值。

1. 開啟 [資源群組]。
2. 選取 [顯示設定] 面板中的**設定**。
3. 選取 [顯示部署歷程記錄面板**部署**]。
4. 選取 [顯示部署的詳細資料的部署]。
5. 選取**匯出的範本**以顯示 [範本] 面板。
6. 選取要匯出的.zip 檔案包含範本參數，與 PowerShell 檔案的 [**儲存至檔案**。

匯出檔案之後，您需要修改。 編輯 parameters.json 檔案，並移除**adminPassword**項目。 部署指令碼執行時，這會導致密碼的提示。 當您執行的部署指令碼時，您可能必須修正空的參數值。

若要使用下載的範本更新設定︰

1. 擷取至您的本機電腦上的資料夾內容。
2. 修改內容，以反映新的設定。
3. 啟動 PowerShell，並變更您用來擷取內容的資料夾。
4. 執行**deploy.ps1** ，並填入訂閱識別碼、 資源群組的名稱 （使用相同的名稱，即可更新設定） 及部署唯一名稱。


### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>部署使用 Azure 資源管理員的診斷副檔名叢集建立的一部分
若要建立叢集使用資源管理員，您需要新增診斷設定 JSON 完整叢集資源管理員範本，建立叢集之前。 我們提供範例五 VM 叢集資源管理員範本診斷設定將它做為我們的資源管理員範本範例的一部分。 您可以在這個位置 Azure 範例圖庫中看到它︰[五節點叢集診斷資源管理員範本的範例](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)。

若要查看的資源管理員範本的 [診斷設定，開啟 azuredeploy.json 檔案**IaaSDiagnostics**搜尋。 若要使用此範本建立叢集，選取 [**部署至 Azure** ] 按鈕在先前的連結。

或者，您可以在此下載資源管理員範例、 進行變更，並修改的範本以建立叢集，使用`New-AzureRmResourceGroupDeployment`PowerShell 的 Azure 視窗中的命令。 請參閱下列程式碼中傳送至命令參數。 如需如何使用 PowerShell 來部署資源群組的詳細資訊，請參閱本文[部署 Azure 資源管理員範本的資源群組](../resource-group-template-deploy.md)。

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>將現有的叢集部署診斷副檔名
如果您有現有的叢集，沒有診斷部署，或如果您想要修改現有的設定，您可以新增或更新。 修改用來建立現有叢集，或從入口網站下載範本，如下所述的資源管理員範本。 藉由執行下列工作修改 template.json 檔案。

新增資源一節，新的儲存空間資源新增至範本。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 接下來，新增到 [參數] 區段只在儲存帳戶定義之後之間`supportLogStorageAccountName`和`vmNodeType0Name`。 取代預留位置文字，*儲存體帳戶名稱在此*儲存體帳戶名稱。

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
然後，更新`VirtualMachineProfile`template.json 檔案加入副檔名陣列中的下列程式碼區段。 請務必將逗號的開頭或結尾，根據插入的位置。

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": "50000",
            "EtwProviders": {
                "EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

所述，修改 template.json 檔案之後，請為資源管理員範本重新發佈。 如果匯出範本時，執行 deploy.ps1 檔案重新發佈範本。 部署之後，請確定**ProvisioningState** **成功**。


## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>更新診斷來收集及上傳新 EventSource 頻道的記錄
若要更新診斷記錄收集代表您即將部署，現有叢集診斷程式的設定為執行相同的步驟[前一節](#deploywadarm)的新應用程式的新 EventSource 頻道。

更新`EtwEventSourceProviderConfiguration`template.json 檔案的使用新的 EventSource 頻道套用設定之前更新，新增項目中的區段`New-AzureRmResourceGroupDeployment`PowerShell 命令。 事件來源名稱定義為 Visual Studio 產生 ServiceEventSource.cs 檔案中的程式碼的一部分。

例如，如果事件來源名稱為 「 我 Eventsource，新增下列程式碼，從 [我 Eventsource 事件放 MyDestinationTableName 的資料表。

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

收集效能計數器或事件記錄，請使用提供[建立的監控和範本 Azure 資源管理員的診斷與 Windows 虛擬機器](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)中的範例修改資源管理員範本。 然後，重新發佈資源管理員範本。

## <a name="next-steps"></a>後續步驟
若要瞭解什麼事件，您應該尋找問題疑難排解的更多詳細資料，請參閱診斷事件發出[可靠的動作項目](service-fabric-reliable-actors-diagnostics.md)及[可靠的服務](service-fabric-reliable-services-diagnostics.md)。


## <a name="related-articles"></a>相關的文章
* [瞭解如何使用副檔名為診斷收集效能計數器或記錄](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [服務記錄檔分析布料的轉印圖樣解決方案](../log-analytics/log-analytics-service-fabric.md)
