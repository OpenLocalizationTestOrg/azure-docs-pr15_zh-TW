<properties
   pageTitle="使用 PowerShell 服務布料的轉印圖樣應用程式升級 |Microsoft Azure"
   description="本文將引導部署服務布料的轉印圖樣的應用程式、 變更程式碼，以及全面使用 PowerShell 升級體驗。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="service-fabric-application-upgrade-using-powershell"></a>使用 PowerShell 服務布料的轉印圖樣應用程式升級

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
- [Visual Studio](service-fabric-application-upgrade-tutorial.md)

<br/>

最常用的和建議升級的方法是監視輪流升級。  Azure 服務布料的轉印圖樣監視根據健康原則的一組被升級的應用程式健康狀況。 升級後的更新網域 （「 ud 」） 後, 服務布料的轉印圖樣評估應用程式健康狀況，其中一項進行到下一個更新網域或失敗的狀況原則根據升級。

使用受管理原生 Api、 PowerShell 或其他可以執行監視的應用程式升級。 如需執行使用 Visual Studio 升級的指示，請參閱[升級您的應用程式使用 Visual Studio](service-fabric-application-upgrade-tutorial.md)。

使用服務布料的轉印圖樣監視循環升級，應用程式管理員可以設定服務布料的轉印圖樣用來決定是否應用程式的健全狀況評估原則。 此外，管理員可以設定 （例如，執行自動回復。） 失敗的狀況評估時所要採取的動作本節將引導其中一個 SDK 範例使用 PowerShell 監視升級。

## <a name="step-1-build-and-deploy-the-visual-objects-sample"></a>步驟 1︰ 建立及部署視覺物件範例


建立並發佈應用程式，以滑鼠右鍵按一下該應用程式的專案， **VisualObjectsApplication，**選取 [**發佈**] 命令。  如需詳細資訊，請參閱[升級教學課程的服務布料的轉印圖樣應用程式](service-fabric-application-upgrade-tutorial.md)。  或者，您可以使用 PowerShell 部署應用程式。

> [AZURE.NOTE] 在 PowerShell 中可能會使用任何服務布料的轉印圖樣命令之前，您必須先使用連線至叢集`Connect-ServiceFabricCluster`指令程式。 同樣地，則會引用本身的叢集已設定您的本機電腦上。 請參閱在[您的服務布料的轉印圖樣的開發環境的設定](service-fabric-get-started.md)。

在建置之後在 Visual Studio 專案，您可以使用 PowerShell 命令**複製 ServiceFabricApplicationPackage**將複製 ImageStore 應用程式套件。 下一步是註冊服務布料的轉印圖樣執行階段使用**Register ServiceFabricApplicationPackage**指令程式的應用程式。 最後一個步驟是使用**新 ServiceFabricApplication**指令程式來啟動應用程式的執行個體。  這三個步驟是類似於 Visual Studio 中使用 [**部署**功能表項目。

現在，您可以使用[服務布料的轉印圖樣總管]，檢視叢集和應用程式](service-fabric-visualizing-your-cluster.md)。 應用程式有可以瀏覽至在 Internet Explorer 網址列中輸入[http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) web 服務。  您應該會看到螢幕中移動一些浮動視覺物件。  此外，您可以使用**取得 ServiceFabricApplication** ，查看應用程式的狀態。

## <a name="step-2-update-the-visual-objects-sample"></a>步驟 2︰ 更新視覺物件範例

您可能會發現的部署步驟 1 中的版本，視覺物件不會。 現在就讓我們升級至單一視覺物件也旋轉此應用程式。

選取 VisualObjects.ActorService 方案中的專案 VisualObjects，並開啟 StatefulVisualObjectActor.cs 檔案。 在該檔案中，瀏覽方法`MoveObject`，註解`this.State.Move()`，並取消註解`this.State.Move(true)`。 服務升級之後，這項變更會旋轉物件。

我們也需要更新專案**VisualObjects.ActorService** *ServiceManifest.xml*檔案 （在下 PackageRoot)。 更新*CodePackage*與服務版本 2.0，並*ServiceManifest.xml*檔案中的相對應的線條。
您以滑鼠右鍵按一下方案進行資訊清單檔案的變更之後，您可以使用 [Visual Studio*編輯資訊清單的檔案*] 選項。


在進行變更之後，資訊清單看起來應該像下列 （醒目提示的部分會顯示所做的變更）︰

```xml
<ServiceManifestName="VisualObjects.ActorService" Version="2.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code" Version="2.0">
```

現在*ApplicationManifest.xml*檔案 （在下**VisualObjects**解決方案**VisualObjects**專案底下找到） 更新版本 2.0 的**VisualObjects.ActorService**專案。 此外，應用程式版本會從 1.0.0.0 更新為 2.0.0.0。 *ApplicationManifest.xml*看起來應該像下列程式碼片段︰

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VisualObjects" ApplicationTypeVersion="2.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService" ServiceManifestVersion="2.0" />
```


現在，選取剛才**ActorService**專案，然後以滑鼠右鍵按一下與 Visual Studio 中選取 [**建立**] 選項，以建立專案。 如果您選取 [**全部重建**] 時，您應該更新適用於所有專案，版本，因為可能變更程式碼。 下一步] 現在就讓我們***VisualObjectsApplication***上按一下滑鼠右鍵，選取 [服務布料的轉印圖樣] 功能表中，然後選擇 [**套件**封裝更新的應用程式。 這個動作會建立可以部署的應用程式套件。  應用程式更新已準備好要部署。


## <a name="step-3--decide-on-health-policies-and-upgrade-parameters"></a>步驟 3︰ 決定健康原則，並升級參數

熟悉的[應用程式升級參數](service-fabric-application-upgrade-parameters.md)並[升級程序](service-fabric-application-upgrade.md)以取得深入了解各種升級參數、 逾時及套用的狀況準則。 此逐步解說，服務健康狀況評估準則設為預設值 （而建議使用） 表示的所有服務和執行個體應該都是_健全_升級後的值。  

不過，讓我們來增加為 60 秒*HealthCheckStableDuration* （使服務健康至少 20 秒升級繼續執行下一個更新網域之前）。  現在就讓我們也設定為 1200 秒*UpgradeDomainTimeout* *UpgradeTimeout*為 3000 的秒數。

最後，我們也設定*UpgradeFailureAction*復原。 這個選項要求服務回復到前一版的應用程式，如果在升級過程中遇到任何問題布料的轉印圖樣。 因此，開始升級 （在步驟 4)，當被指定下列參數︰

FailureAction = 復原

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200年

UpgradeTimeout = 3000


## <a name="step-4-prepare-application-for-upgrade"></a>步驟 4︰ 準備升級的應用程式

現在應用程式可以建立並準備好升級。 如果您為系統管理員和類型**取得 ServiceFabricApplication**開啟 PowerShell 視窗，它應該可讓您知道**VisualObjects**已部署的應用程式類型 1.0.0.0。  

應用程式套件就會儲存在以下位置您未壓縮服務布料的轉印圖樣 SDK- *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*的相對路徑。 您應該在該目錄中，儲存應用程式套件的位置尋找 「 套件 」 資料夾。 檢查以確保它是最新版本 （您可能需要以及視情況修改路徑） 的時間戳記。

現在讓我們來複製到 （應用程式套件的儲存位置的服務布料的轉印圖樣） 服務布料的轉印圖樣 ImageStore 的 [更新應用程式套件。 參數*ApplicationPackagePathInImageStore*通知服務布料的轉印圖樣它可以在哪裡找到應用程式套件。 我們已將更新的應用程式放在 「 VisualObjects\_V2 「 使用下列命令 （您可能需要一次視情況修改路徑）。

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

下一步是登錄服務結構，可以使用下列命令來執行這個應用程式︰

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

如果不成功之前的命令，則可能必須重建所有服務。 步驟 2 所述，您可能必須更新您 WebService 版本。

## <a name="step-5-start-the-application-upgrade"></a>步驟 5︰ 啟動應用程式的升級

現在，我們已完成所有設定要使用下列命令來啟動應用程式升級︰

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


應用程式的名稱就相同如下所述*ApplicationManifest.xml*檔案。 服務布料的轉印圖樣會使用此名稱來識別哪些應用程式開始升級。 如果您設定為太短逾時，您可能會遇到問題的錯誤訊息。 請參閱疑難排解的部分，或增加逾時。

現在，應用程式升級進行，您可以監視並使用服務布料的轉印圖樣總管] 中，或使用下列 PowerShell 命令︰**取得 ServiceFabricApplicationUpgrade 布料的轉印圖樣: / VisualObjects**。

在幾分鐘，您有使用上述的 PowerShell 命令，狀態應該狀態更新的所有網域已都升級 （完成）。 您應該找出您的瀏覽器視窗中的視覺物件已經開始旋轉 ！

您可以嘗試從版本 2 到版本 3，或從版本練習 1 至 2 版升級。 從第 2 移到第 1 版也會被視為升級。 播放逾時與健康原則，讓自己熟悉這些。 當您部署到 Azure 叢集時，參數必須正確設定。 最好保守設定逾時。


## <a name="next-steps"></a>後續步驟

[升級您的應用程式使用 Visual Studio](service-fabric-application-upgrade-tutorial.md)逐步引導您進行使用 Visual Studio 應用程式升級。

控制您的應用程式使用[升級參數](service-fabric-application-upgrade-parameters.md)升級的方式。

學習如何使用[資料序列化](service-fabric-application-upgrade-data-serialization.md)，讓您的應用程式升級相容。

瞭解如何時參考[進階的主題](service-fabric-application-upgrade-advanced.md)升級您的應用程式使用進階的功能。

參照中[疑難排解應用程式升級](service-fabric-application-upgrade-troubleshooting.md)的步驟，在應用程式升級中修正常見問題的解決辦法。
