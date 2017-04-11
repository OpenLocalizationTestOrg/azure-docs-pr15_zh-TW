<properties
    pageTitle="使用 PowerShell 自動化服務布料的轉印圖樣應用程式管理 |Microsoft Azure"
    description="部署、 升級、 測試和使用 PowerShell 移除服務布料的轉印圖樣應用程式。"
    services="service-fabric"
    documentationCenter=".net"
    authors="rwike77"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-fabric"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="ryanwi"/>

# <a name="automate-the-application-lifecycle-using-powershell"></a>自動化使用 PowerShell 應用程式生命週期

可以自動化[服務布料的轉印圖樣應用程式生命週期](service-fabric-application-lifecycle.md)的許多方面。  本文說明如何使用 PowerShell 來自動化部署、 升級、 移除及測試 Azure 服務布料的轉印圖樣應用程式的一般工作。  管理與應用程式管理 HTTP Api，也會出現。 [應用程式生命週期](service-fabric-application-lifecycle.md)的詳細資訊，請參閱。  

## <a name="prerequisites"></a>必要條件
您移到文件中的工作之前，請務必︰

+ 熟悉[技術概觀服務布料的轉印圖樣](service-fabric-technical-overview.md)所述的服務布料的轉印圖樣概念。
+ [安裝執行階段、 sdk，您可以及工具](service-fabric-get-started.md)，其中也會安裝**ServiceFabric** PowerShell 模組。
+ [啟用的 PowerShell 指令碼執行](service-fabric-get-started.md#enable-powershell-script-execution)。
+ 啟動本機叢集。  啟動新的 PowerShell 視窗以系統管理員身分，然後再執行 SDK 資料夾中的 [叢集安裝指令碼︰`& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"`
+ 在執行本文中的任何 PowerShell 命令之前，先使用連線到本機服務布料的轉印圖樣叢集[**連線 ServiceFabricCluster**](https://msdn.microsoft.com/library/azure/mt125938.aspx):`Connect-ServiceFabricCluster localhost:19000`
+ 下列工作需要升級 v1 應用程式套件部署及 v2 應用程式套件。 下載[**WordCount**範例應用程式](http://aka.ms/servicefabricsamples)（位於快速入門範例）。 建立並封裝在 Visual Studio 中 （在方案總管] 和 [選取**套件** **WordCount**上以滑鼠右鍵按一下） 應用程式。 複製 v1 套件中的`C:\ServiceFabricSamples\Services\WordCount\WordCount\pkg\Debug`至`C:\Temp\WordCount`。 複製`C:\Temp\WordCount`至`C:\Temp\WordCountV2`，建立 [升級 v2 應用程式套件。 開啟`C:\Temp\WordCountV2\ApplicationManifest.xml`文字編輯器中。 **ApplicationManifest**項目中變更**ApplicationTypeVersion**屬性從 「 1.0.0 」 以 「 2.0.0 「 若要更新的應用程式版本號碼。 儲存變更的 ApplicationManifest.xml 檔案。

## <a name="task-deploy-a-service-fabric-application"></a>工作︰ 部署服務布料的轉印圖樣應用程式

您已建立及封裝應用程式 （或下載應用程式套件之後），您可以部署到本機的服務布料的轉印圖樣叢集應用程式。 部署涉及應用程式套件上傳、 登錄應用程式類型，並建立的應用程式執行個體。 使用此區段中的指示，部署至叢集新的應用程式。

### <a name="step-1-upload-the-application-package"></a>步驟 1︰ 將應用程式套件上傳
將應用程式套件上傳至圖像存放區會將其放在一個位置存取內部服務布料的轉印圖樣元件。  應用程式套件包含必要的應用程式資訊清單與服務的資訊清單的程式碼，設定，以及要建立的應用程式及服務執行個體的資料套件。  [**複製 ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125905.aspx) ] 命令上傳套件。 例如︰

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCount\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

### <a name="step-2-register-the-application-type"></a>步驟 2︰ 登錄應用程式類型
註冊應用程式套件，可讓您在應用程式類型及宣告可用應用程式資訊清單中使用的版本。 系統會讀取套件上傳在步驟 1，驗證套件 （相當於本機執行[**測試 ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt125950.aspx) ）、 封裝內容]、 [處理程序和處理的封裝複製到 [是內部系統位置。  執行[**Register ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx) cmdlet:

```powershell
Register-ServiceFabricApplicationType WordCount
```
若要查看所有應用程式類型中叢集註冊，請執行[取得 ServiceFabricApplicationType](https://msdn.microsoft.com/library/azure/mt125871.aspx)指令程式︰

```powershell
Get-ServiceFabricApplicationType
```

### <a name="step-3-create-the-application-instance"></a>步驟 3︰ 建立應用程式執行個體
應用程式，可以使用任何已經順利註冊使用 [[**新增 ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125913.aspx) ] 命令的應用程式類型版本產生。 部署時間，而且必須以開頭，每個應用程式名稱在宣告**布料的轉印圖樣︰**配置和是唯一的每個應用程式執行個體。 [應用程式類型名稱] 和 [應用程式類型版本宣告應用程式套件的**ApplicationManifest.xml**檔案中。 如果任何預設的服務應用程式顯示的目標應用程式類型中定義，這些會建立這一次。

```powershell
New-ServiceFabricApplication fabric:/WordCount WordCount 1.0.0
```

[**取得 ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx)命令可列出所有應用程式執行個體順利建立的以及他們整體的狀態。 [**取得 ServiceFabricService**](https://msdn.microsoft.com/library/azure/mt125889.aspx)命令可列出所有服務執行個體中指定的應用程式的執行個體順利建立的。 預設服務 （如果有的話）） 列。

```powershell
Get-ServiceFabricApplication

Get-ServiceFabricApplication | Get-ServiceFabricService
```

## <a name="task-upgrade-a-service-fabric-application"></a>工作︰ 升級服務布料的轉印圖樣應用程式
您可以升級先前部署的服務布料的轉印圖樣應用程式與更新的應用程式套件。 這項工作升級 WordCount 應用程式部署中的 「 工作︰ 部署服務布料的轉印圖樣應用程式。 」 如需詳細資訊，請閱讀透過[服務布料的轉印圖樣應用程式升級](service-fabric-application-upgrade.md)。

若要保留項目簡單這個範例中，只應用程式版本號碼已更新 WordCountV2 應用程式套件的先決條件中建立。 更新您的服務代碼、 設定或資料檔案然後重建並包裝更新的版本的數字的應用程式，可能包含更真實的案例。  

### <a name="step-1-upload-the-updated-application-package"></a>步驟 1︰ 將更新的應用程式套件上傳
WordCount v1 應用程式已準備好升級。 如果您為系統管理員]，然後輸入[**取得 ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt163515.aspx)開啟 PowerShell 視窗，您會看到部署版本 1.0.0 WordCount 應用程式類型。  

現在複製更新應用程式套件 （應用程式套件的儲存位置的服務布料的轉印圖樣） 服務布料的轉印圖樣圖像存放區。 參數**ApplicationPackagePathInImageStore**通知服務布料的轉印圖樣它可以在哪裡找到應用程式套件。 下列命令會複製到**WordCountV2**圖像存放區中的應用程式套件︰  

```powershell
Copy-ServiceFabricApplicationPackage C:\Temp\WordCountV2\ -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCountV2

```
### <a name="step-2-register-the-updated-application-type"></a>步驟 2︰ 註冊更新的應用程式類型
下一步是註冊新版本的應用程式與服務結構，可以使用[**登錄 ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125958.aspx)指令程式來執行︰

```powershell
Register-ServiceFabricApplicationType WordCountV2
```

### <a name="step-3-start-the-upgrade"></a>步驟 3︰ 開始升級
各種升級參數逾時與狀況的準則，都可以套用到應用程式升級。 閱讀，瞭解更多的[應用程式升級參數](service-fabric-application-upgrade-parameters.md)和[升級程序](service-fabric-application-upgrade.md)文件。 所有的服務與執行個體應該_健全_升級之後。  （使服務健康至少升級繼續執行下一個升級網域之前 20 秒），則您可以將 60 秒**HealthCheckStableDuration** 。  也設定**UpgradeDomainTimeout** 1200 秒和**UpgradeTimeout** 3000 秒。 最後，設定**UpgradeFailureAction** **復原**，要求的服務布料的轉印圖樣復原先前版本的應用程式如果升級過程中發生失敗。

您現在可以使用[**開始 ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125975.aspx)指令程式來啟動應用程式升級︰

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/WordCount -ApplicationTypeVersion 2.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000  -FailureAction Rollback -Monitored
```

請注意，應用程式的名稱與先前部署的 v1.0.0 應用程式的名稱相同 (布料的轉印圖樣: / WordCount)。 服務布料的轉印圖樣會使用這個名稱來識別哪些應用程式開始升級。 如果您設定為太短逾時，您可能會遇到逾時錯誤訊息，指出問題。 [疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)，請參閱，或增加逾時。

### <a name="step-4-check-upgrade-progress"></a>步驟 4︰ 核取升級進度
您可以監視應用程式升級進度，藉由使用[服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)] 中，或使用[**取得 ServiceFabricApplicationUpgrade**](https://msdn.microsoft.com/library/azure/mt125988.aspx)指令程式︰

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/WordCount
```

在幾分鐘，[取得 ServiceFabricApplicationUpgrade](https://msdn.microsoft.com/library/azure/mt125988.aspx)指令程式會顯示所有升級的網域已升級 （完成）。

## <a name="task-test-a-service-fabric-application"></a>工作︰ 測試服務布料的轉印圖樣應用程式

若要撰寫高品質的服務，開發人員需要能夠便會以測試其服務的穩定性不可靠的基礎結構錯誤。 服務布料的轉印圖樣可讓開發人員產生錯誤的動作，並使用混亂和容錯移轉測試案例測試失敗時的服務。  如需詳細資訊，請閱讀透過[可測試性概觀](service-fabric-testability-overview.md)。

### <a name="step-1-run-the-chaos-test-scenario"></a>步驟 1︰ 執行混亂測試案例
混亂測試案例整個服務布料的轉印圖樣叢集產生錯誤。 此案例壓縮透過月或年幾個小時常見的錯誤。 高故障率交錯錯誤的組合會找出角會遺漏的情況。 下列範例會執行混亂測試案例 60 分鐘的時間。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```

### <a name="step-2-run-the-failover-test-scenario"></a>步驟 2︰ 執行容錯移轉測試案例
容錯移轉測試案例目標特定服務磁碟分割，而非整個，它會保留其他服務不會受到影響。 此案例逐一執行您的商務邏輯時的模擬服務驗證錯誤的順序。 在服務驗證失敗表示需要進一步調查問題。 錯誤後移轉測試引發只有一個錯誤，一次，而不是混亂測試案例，可以產生多個錯誤。 下列範例會針對布料的轉印圖樣執行 60 分鐘的容錯移轉測試: / WordCount/WordCountService 服務。

```powershell
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/WordCount/WordCountService"

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindUniformInt64 -PartitionKey 1
```

## <a name="task-remove-a-service-fabric-application"></a>工作︰ 移除服務布料的轉印圖樣的應用程式
您可以刪除部署的應用程式的執行個體，能夠應用程式類型中移除叢集，並從 ImageStore 移除應用程式套件。

### <a name="step-1-remove-an-application-instance"></a>步驟 1︰ 移除應用程式執行個體
當已不再需要的應用程式執行個體時，您可以使用[**移除 ServiceFabricApplication**](https://msdn.microsoft.com/library/azure/mt125914.aspx)指令程式來永久移除它。 這也會自動會移除所有服務屬於應用程式，將會永久移除所有的服務狀態。 無法回復這項作業，而且無法復原應用程式狀態。

```powershell
Remove-ServiceFabricApplication fabric:/WordCount
```

### <a name="step-2-unregister-the-application-type"></a>步驟 2︰ 移除註冊應用程式類型
當您不再需要特定版本的應用程式類型時，請使用[**取消註冊 ServiceFabricApplicationType**](https://msdn.microsoft.com/library/azure/mt125885.aspx)指令程式來註冊。 取消註冊未使用過的類型釋放使用應用程式套件圖像存放區中的儲存空間。 只要沒有產生針對] 或 [擱置參考的應用程式升級的應用程式，可以取消登錄應用程式類型。

```powershell
Unregister-ServiceFabricApplicationType WordCount 1.0.0
```

### <a name="step-3-remove-the-application-package"></a>步驟 3︰ 移除應用程式套件
未註冊應用程式類型後，可以從映像存放區移除應用程式套件，使用[**移除 ServiceFabricApplicationPackage**](https://msdn.microsoft.com/library/azure/mt163532.aspx)指令程式。

```powershell
Remove-ServiceFabricApplicationPackage -ImageStoreConnectionString file:C:\SfDevCluster\Data\ImageStoreShare -ApplicationPackagePathInImageStore WordCount
```

## <a name="next-steps"></a>後續步驟
[服務布料的轉印圖樣應用程式生命週期](service-fabric-application-lifecycle.md)

[部署應用程式](service-fabric-deploy-remove-applications.md)

[應用程式升級](service-fabric-application-upgrade.md)

[Azure 服務布料的轉印圖樣 cmdlet](https://msdn.microsoft.com/library/azure/mt125965.aspx)

[Azure 服務布料的轉印圖樣可測試性 cmdlet](https://msdn.microsoft.com/library/azure/mt125844.aspx)
