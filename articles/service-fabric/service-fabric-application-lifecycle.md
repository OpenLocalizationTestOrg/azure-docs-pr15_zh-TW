<properties
   pageTitle="應用程式中服務布料的轉印圖樣的生命週期 |Microsoft Azure"
   description="說明開發、 部署、 測試、 升級、 維護及移除服務布料的轉印圖樣應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="service-fabric-application-lifecycle"></a>服務布料的轉印圖樣應用程式生命週期
與其他平台，Azure 服務布料的轉印圖樣上的應用程式通常會執行下列階段如下︰ 設計、 開發、 測試、 部署、 升級、 維護及移除。 服務架構提供完整的應用程式的生命週期雲端應用程式，從開發到部署、 每日管理，以及進行的維修作業最終解除委任最高級支援。 服務模型可讓獨立參與應用程式生命週期數種不同的角色。 本文提供 Api 及如何使用整個服務布料的轉印圖樣應用程式生命週期的時期的不同角色的概觀。

## <a name="service-model-roles"></a>服務模型角色
服務模型角色為︰

- **服務開發人員**︰ 開發模組化和一般的服務，來組織及類型相同或不同類型的多個應用程式中使用。 例如，佇列中服務可用於建立票券的應用程式 （技術服務） 或電子商務應用程式 （購物車）。

- **應用程式開發人員**︰ 整合服務，以符合特定的特定需求或案例的集合建立應用程式。 例如，電子商務網站可能 」 JSON 無前端與服務整合，「 」 拍賣狀態服務 」 和 「 佇列中狀態服務 」 建置 auctioning 的解決方案。

- **應用程式管理員**︰ 可讓您的應用程式設定 （填寫設定範本參數）、 部署 （對應可用資源） 和服務的品質的決策。 例如，應用程式的系統管理員會決定語言的地區設定 （英文美國） 或日文版日本，例如應用程式。 不同的部署應用程式可以有不同的設定。

- **運算子**︰ 部署應用程式設定為基礎的應用程式與應用程式的系統管理員所指定的需求。 例如，運算子佈建新的部署應用程式，並確保執行 Azure 中。 運算子監視應用程式的健康狀況和效能的資訊，並視需要維護的實體基礎結構。


## <a name="develop"></a>開發
1. *服務開發人員*開發不同類型的服務使用[可靠的動作項目](service-fabric-reliable-actors-introduction.md)或[可靠的服務](service-fabric-reliable-services-introduction.md)程式設計模型。
2. *服務開發人員*宣告方式描述的開發的服務類型中所組成的一或多個程式碼、 設定及資料封包的服務資訊清單的檔案。
3. *應用程式開發人員*，然後建立使用不同的服務類型的應用程式。
4. *應用程式開發人員*宣告方式說明應用程式資訊清單中的應用程式類型參照組成的服務與適當覆寫和參數化組成服務的不同設定及部署設定的服務資訊清單。

如需範例，請參閱[開始使用可靠的動作項目](service-fabric-reliable-actors-get-started.md)，以及[快速入門可靠的服務](service-fabric-reliable-services-quick-start.md)。

## <a name="deploy"></a>部署
1. *應用程式管理員*可調整部署至服務布料的轉印圖樣叢集應用程式顯示指定**ApplicationType**元素的適當的參數，以特定的應用程式的應用程式類型。

2. *運算子*使用[**CopyApplicationPackage**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx)或[**複製 ServiceFabricApplicationPackage**指令程式](https://msdn.microsoft.com/library/azure/mt125905.aspx)上傳至叢集圖像市集的應用程式套件。 應用程式套件包含應用程式顯示及服務套件的集合。 服務布料的轉印圖樣部署儲存市集中的圖像，可以是 Azure blob 儲存或服務布料的轉印圖樣系統服務應用程式套件的應用程式。

3. *運算子*然後佈建新的目標叢集使用[**ProvisionApplicationAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx)、 [ **Register ServiceFabricApplicationType**指令程式](https://msdn.microsoft.com/library/azure/mt125958.aspx)或[**佈建應用程式**的其餘部分作業](https://msdn.microsoft.com/library/azure/dn707672.aspx)的上傳的應用程式套件的應用程式類型。

4. 佈建應用程式之後,*運算子*會啟動應用程式，以使用[**CreateApplicationAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx)、[**新增 ServiceFabricApplication**指令程式](https://msdn.microsoft.com/library/azure/mt125913.aspx)或[**建立應用程式**的其餘部分作業](https://msdn.microsoft.com/library/azure/dn707676.aspx)*應用程式管理員*所提供的參數。

5. 部署應用程式後，*運算子*，則會使用[**CreateServiceAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx)、[**新增 ServiceFabricService**指令程式](https://msdn.microsoft.com/library/azure/mt125874.aspx)或[**建立服務**其餘作業](https://msdn.microsoft.com/library/azure/dn707657.aspx)來建立新的服務執行個體，根據 [可用的服務類型的應用程式。

6. 在服務布料的轉印圖樣叢集立即執行應用程式。

如需範例，請參閱[部署應用程式](service-fabric-deploy-remove-applications.md)。

## <a name="test"></a>測試
1. 部署至本機開發叢集或測試叢集之後,*服務開發人員*，請使用[**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx)和[**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx)類別，或是[**叫用 ServiceFabricFailoverTestScenario**指令程式](https://msdn.microsoft.com/library/azure/mt644783.aspx)的執行內建的容錯移轉測試案例。 容錯移轉測試案例中執行的指定的服務，透過重要的轉場效果與容錯移轉，以確保仍可使用及工作。

2. *服務開發人員*，然後執行內建混亂測試案例中使用[**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx)和[**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx)類別，或是[**叫用 ServiceFabricChaosTestScenario**指令程式](https://msdn.microsoft.com/library/azure/mt644774.aspx)。 混亂測試案例隨機引發多個節點、 程式碼套件和複本錯誤到叢集。

3. *服務開發人員*[測試服務-服務通訊](service-fabric-testability-scenarios-service-communication.md)來撰寫移動叢集周圍的主要複本的測試方案。

如需詳細資訊，請參閱[簡介故障分析服務](service-fabric-testability-overview.md)。

## <a name="upgrade"></a>升級
1. *服務開發人員*更新組成的例項的應用程式服務及/或修正錯誤，並提供服務資訊清單的新版本。

2. *應用程式開發人員*會覆寫參數化設定及部署設定的一致的服務，並提供的應用程式顯示新版本。 應用程式開發人員然後整合至應用程式的 [服務資訊清單的新版本，並提供新版本的應用程式類型中更新的應用程式套件。

3. *應用程式管理員*會更新適當的參數來合併至目標應用程式的新版本的應用程式類型。

5. *運算子*上傳至叢集圖像存放區使用[**CopyApplicationPackage**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx)或[**複製 ServiceFabricApplicationPackage**指令程式](https://msdn.microsoft.com/library/azure/mt125905.aspx)的更新應用程式套件。 應用程式套件包含應用程式顯示及服務套件的集合。

6. 使用[**ProvisionApplicationAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx)、 [ **Register ServiceFabricApplicationType**指令程式](https://msdn.microsoft.com/library/azure/mt125958.aspx)或[**佈建應用程式**的其餘部分作業](https://msdn.microsoft.com/library/azure/dn707672.aspx)*運算子*佈建新的目標叢集的應用程式的新版本。

7. *運算子*升級為使用[**UpgradeApplicationAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx)、[**開始 ServiceFabricApplicationUpgrade**指令程式](https://msdn.microsoft.com/library/azure/mt125975.aspx)或[**升級應用程式**的其餘部分作業](https://msdn.microsoft.com/library/azure/dn707633.aspx)的新版本的目標應用程式。

8. *運算子*會檢查使用[**GetApplicationUpgradeProgressAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx)、[**取得 ServiceFabricApplicationUpgrade**指令程式](https://msdn.microsoft.com/library/azure/mt125988.aspx)或[**取得應用程式升級進行**其他操作](https://msdn.microsoft.com/library/azure/dn707631.aspx)的升級的進度。

9. 如有必要，*運算子*修改，並重新套用目前的應用程式升級使用[**UpdateApplicationUpgradeAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx)、[**更新 ServiceFabricApplicationUpgrade**指令程式](https://msdn.microsoft.com/library/azure/mt126030.aspx)或[**更新應用程式升級**的其餘部分作業](https://msdn.microsoft.com/library/azure/mt628489.aspx)的參數。

10. 如有必要，*運算子*復原目前的應用程式升級使用[**RollbackApplicationUpgradeAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx)、[**開始 ServiceFabricApplicationRollback**指令程式](https://msdn.microsoft.com/library/azure/mt125833.aspx)或[**復原應用程式升級**的其餘部分作業](https://msdn.microsoft.com/library/azure/mt628494.aspx)。

11. 服務布料的轉印圖樣升級叢集中執行，而不遺失的任何組成的服務可用性目標應用程式。

請參閱[升級教學課程的應用程式](service-fabric-application-upgrade-tutorial.md)的範例。

## <a name="maintain"></a>維護
1. 作業系統升級及修補程式，服務布料的轉印圖樣介面 Azure 基礎結構，以確保在叢集中執行的所有應用程式的可用性。

2. 升級及修補程式服務布料的轉印圖樣平台，服務布料的轉印圖樣升級本身而不會遺失任何叢集上執行應用程式的可用性。

3. *應用程式管理員*核准的新增或移除的節點的分析歷史容量使用情況資料和預計未來的要求之後。

4. *運算子*加入，及移除*應用程式管理員*所指定的節點。

5. 當新的節點會新增至或現有的節點移除了叢集時，服務布料的轉印圖樣自動負載平衡執行應用程式中叢集達到最佳效能的所有節點。

## <a name="remove"></a>移除
1. *運算子*可刪除叢集執行服務的特定執行個體，而不移除整個應用程式使用[**DeleteServiceAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx)、[**移除 ServiceFabricService**指令程式](https://msdn.microsoft.com/library/azure/mt126033.aspx)或[**刪除服務**其餘作業](https://msdn.microsoft.com/library/azure/dn707687.aspx)。  

2. 應用程式執行個體及所有服務使用[**DeleteApplicationAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx)、[**移除 ServiceFabricApplication**指令程式](https://msdn.microsoft.com/library/azure/mt125914.aspx)或[**都刪除應用程式**的其餘部分作業](https://msdn.microsoft.com/library/azure/dn707651.aspx)，也可以都刪除*運算子*。

3. 一旦應用程式及服務都已停止]，*運算子*，可以取消提供使用[**UnprovisionApplicationAsync**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx)、[**取消註冊 ServiceFabricApplicationType**指令程式](https://msdn.microsoft.com/library/azure/mt125885.aspx)或[**Unprovision 應用程式**的其餘部分作業](https://msdn.microsoft.com/library/azure/dn707671.aspx)的應用程式類型。 取消提供的應用程式類型並不會從 ImageStore 移除應用程式套件。 您必須以手動方式移除應用程式套件。

4. *運算子*使用[**RemoveApplicationPackage**方法](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx)或[**移除 ServiceFabricApplicationPackage** cmdlet](https://msdn.microsoft.com/library/azure/mt163532.aspx)ImageStore 中移除應用程式套件。

如需範例，請參閱[部署應用程式](service-fabric-deploy-remove-applications.md)。

## <a name="next-steps"></a>後續步驟

如需開發的詳細資訊，請測試和管理服務布料的轉印圖樣應用程式和服務，請參閱︰

- [可靠的動作項目](service-fabric-reliable-actors-introduction.md)
- [可靠的服務](service-fabric-reliable-services-introduction.md)
- [部署應用程式](service-fabric-deploy-remove-applications.md)
- [應用程式升級](service-fabric-application-upgrade.md)
- [可測試性概觀](service-fabric-testability-overview.md)
- [其他應用程式生命週期範例](service-fabric-rest-based-application-lifecycle-sample.md)
