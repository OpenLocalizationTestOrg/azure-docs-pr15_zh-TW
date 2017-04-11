<properties
   pageTitle="開始使用部署與升級您的本機叢集上的應用程式 |Microsoft Azure"
   description="設定本機的服務布料的轉印圖樣叢集部署，現有的應用程式，然後升級的應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>部署及升級您的本機叢集上的應用程式快速入門
Azure 服務布料的轉印圖樣 SDK 包含可用來快速入門部署及管理本機叢集上的應用程式的完整本機開發環境。 本文中，您可以建立本機叢集、 部署現有的應用程式，並再升級至新的版本，所有的 Windows PowerShell 的應用程式。

> [AZURE.NOTE] 本文假設您已[設定您的開發環境](service-fabric-get-started.md)。

## <a name="create-a-local-cluster"></a>建立本機叢集
服務布料的轉印圖樣叢集代表一組硬體資源可以部署應用程式。 一般而言，叢集是組成的任何位置的五個至數以千計的電腦。 不過，服務布料的轉印圖樣 SDK 包括叢集設定可以在一部電腦上執行。

請務必瞭解服務布料的轉印圖樣本機叢集不是模擬器。 執行相同的平台程式碼的多機器叢集上找到。 唯一的差別執行一般跨越一部電腦上的五個電腦的平台處理程序。

SDK 提供兩種方法可以設定本機叢集︰ Windows PowerShell 指令碼和本機叢集管理員系統匣應用程式。 在本教學課程中，我們使用 PowerShell 指令碼。

> [AZURE.NOTE] 如果您已經部署應用程式從 Visual Studio 建立本機叢集，您可以略過此節。


1. 以系統管理員身分啟動新的 PowerShell 視窗。

2. 從 SDK 資料夾執行叢集安裝指令碼︰

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    叢集安裝需要一些時間。 安裝完成後，您應該會看到類似的輸出︰

    ![叢集設定輸出][cluster-setup-success]

    您已準備好要嘗試部署叢集應用程式。

## <a name="deploy-an-application"></a>部署應用程式
服務布料的轉印圖樣 SDK 包含豐富的架構與開發人員工具來建立應用程式。 如果您想要進一步瞭解如何在 Visual Studio 建立應用程式，請參閱[建立 Visual Studio 中第一個服務布料的轉印圖樣應用程式](service-fabric-create-your-first-application-in-visual-studio.md)。

在本教學課程中，我們將使用現有的範例應用程式 （稱為 WordCount），讓我們可以焦點放在平台，包括部署、 監控及升級的管理層面。


1. 以系統管理員身分啟動新的 PowerShell 視窗。

2. 匯入服務布料的轉印圖樣 SDK PowerShell 模組。

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. 建立以儲存您下載並部署，例如 C:\ServiceFabric 的應用程式目錄。

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. 您建立[下載 WordCount 應用程式](http://aka.ms/servicefabric-wordcountapp)到的位置。  附註︰ Microsoft Edge 瀏覽器會將檔案儲存為*.zip*副檔名。  變更*.sfpkg*副檔名。

5. 連線到本機叢集︰

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. 建立新的應用程式使用 SDK 的部署] 命令的名稱，以及應用程式套件的路徑。

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    如果一切順利，您應該會看到如下所示的輸出︰

    ![部署到本機叢集應用程式][deploy-app-to-local-cluster]

7. 若要查看的應用程式中的動作，啟動瀏覽器並瀏覽至[http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html)。 您應該會看到︰

    ![部署的應用程式使用者介面][deployed-app-ui]

    WordCount 應用程式是非常簡單。 其包含產生隨機五個字元 「 文字 」，然後轉送至 ASP.NET Web API 透過應用程式的用戶端 JavaScript 程式碼。 設定狀態服務追蹤的計算字數。 這些被分割根據單字的第一個字元。 您可以找到 WordCount 應用程式中的[快速入門範例](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/)的程式碼。

    我們部署的應用程式包含四個磁碟分割。 讓文字從 A 到 G 開始儲存在第一個磁碟分割，文字開頭 H 到 N 儲存在第二個分割，等等。

## <a name="view-application-details-and-status"></a>檢視應用程式詳細資料] 與 [狀態
現在，我們已部署應用程式，我們來看看應用程式中的詳細資料 PowerShell 部分。

1. 查詢叢集上的所有已部署應用程式︰

    ```powershell
    Get-ServiceFabricApplication
    ```

    假設您僅有部署 WordCount 應用程式，您會看到類似︰

    ![在 PowerShell 中的所有已部署應用程式的查詢][ps-getsfapp]

2. 藉由查詢的 WordCount 應用程式中包含的服務設定，請移至下一層級。

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![在 PowerShell 中應用程式的清單服務][ps-getsfsvc]

    應用程式組成的兩個服務-web 前端與管理字詞的狀態服務。

3. 最後，查看的磁碟分割之清單的 WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![在 PowerShell 中檢視服務分割][ps-getsfpartitions]

    使用所有服務布料的轉印圖樣 PowerShell 命令，例如的命令集可供您可能會連接至本機或遠端任何叢集。

    互動叢集更視覺化方式，您可以瀏覽至 [[檔案總管](http://localhost:19080/Explorer)http://localhost:19080/瀏覽器中使用 web 服務布料的轉印圖樣總管工具。

    ![在服務布料的轉印圖樣總管] 中檢視應用程式詳細資料][sfx-service-overview]

    > [AZURE.NOTE] 若要瞭解更多關於服務布料的轉印圖樣總管，請參閱[視覺化叢集服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)。

## <a name="upgrade-an-application"></a>升級應用程式
服務布料的轉印圖樣提供無停機時間升級為叢集，它會監視的應用程式的狀況。 讓我們來執行簡單的升級 WordCount 應用程式。

新版本的應用程式現在會計算開頭母音的文字。 當準備升級，我們就會看到兩項變更在 [應用程式的行為。 首先，計數規模擴大時的率應該變慢，因為會被計算較少的字詞。 第二，第一個磁碟分割具有兩個音 （A 與 E），而其他所有磁碟分割區只含有一個每個，因為其計數應該最後開始迎合其他人。

1. [下載 WordCount v2 套件](http://aka.ms/servicefabric-wordcountappv2)，即可下載 v1 套件的所在的相同位置。

2. 返回您 PowerShell 視窗，然後使用 SDK 的升級] 命令，在叢集註冊新的版本。 開始升級布料的轉印圖樣: / WordCount 應用程式。

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    您應該會看到看起來類似下列升級為開始的 PowerShell 中輸出。

    ![在 PowerShell 中升級的進度][ps-appupgradeprogress]

3. 繼續進行升級時，您可能會發現監控其狀態，從服務布料的轉印圖樣總管變得更容易。 啟動瀏覽器視窗，然後瀏覽至[Http://localhost:19080/總管]](http://localhost:19080/Explorer)。 展開 [左側的樹狀目錄中的 [**應用程式**，然後選擇 [ **WordCount**，最後**布料的轉印圖樣: / WordCount**。 在 [基本資訊] 索引標籤中，您會看到升級的狀態如下並在進行透過叢集的升級的網域。

    ![升級服務布料的轉印圖樣檔案總管中的進度][sfx-upgradeprogress]

    在進行時升級到每個網域，以確保應用程式行為正常執行狀況檢查。

4. 如果您重新執行舊版查詢布料的轉印圖樣的服務系列: / WordCount 應用程式，請注意，WordCountService 版本變更但 WordCountWebService 新版並未︰

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![升級後的查詢應用程式服務][ps-getsfsvc-postupgrade]

    這會醒目提示服務布料的轉印圖樣如何管理應用程式升級。 僅限的一組服務 （或在這些服務的程式碼/設定套件） 已變更，讓處理程序的升級速度更快且更可靠碰。

5. 最後，返回瀏覽器，觀察新的應用程式版本的行為。 計數如預期地進行速度變慢，並第一個磁碟分割結束稍有多個音量。

    ![在瀏覽器中檢視應用程式的新版本][deployed-app-ui-v2]

## <a name="cleaning-up"></a>清除

之前文繞圖，請務必記得本機叢集實數。 應用程式繼續在背景中執行，直到您將其移除。  根據您的應用程式的性質，最多大量資源在您的電腦上可能會執行應用程式。 您有幾個選項，來管理應用程式和叢集︰

1. 若要移除個別的應用程式和所有的資料，請執行下列動作︰

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    或者，您也可以刪除服務布料的轉印圖樣總管] 的 [**動作**] 功能表或應用程式清單檢視的左窗格中的 [內容] 功能表中的應用程式。

    ![刪除應用程式是服務布料的轉印圖樣總管][sfe-delete-application]

2. 之後的刪除應用程式，您可以移除註冊 1.0.0 和 2.0.0 WordCount 應用程式類型的版本。 刪除移除應用程式套件，包括程式碼，從叢集的圖像市集的設定。

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    或者，您也可以在服務布料的轉印圖樣總管] 中，選擇 [應用程式的 [ **Unprovision 類型**。

3. 若要關閉 [叢集，但保留應用程式的資料及追蹤，按一下 [在系統匣應用程式中的 [**停止本機叢集**]。

4. 若要完全刪除叢集，按一下 [在系統匣應用程式中的 [**移除本機叢集**]。 這個選項會在另一個變得很慢部署下次當您在 Visual Studio 中按 F5。 只有當您不想要用於一些時間，或如果您要收回資源，請移除本機叢集。

## <a name="1-node-and-5-node-cluster-mode"></a>1 的節點和 5 的節點叢集模式

使用本機叢集開發應用程式工作時，您發現自己執行快速反覆運算撰寫程式碼偵錯、 變更程式碼的偵錯等等。若要協助最佳化此程序，本機叢集可以在兩種模式執行︰ 1 節點或 5 節點。 這兩種叢集模式都有優點。
5 節點叢集模式可讓您使用實數叢集。 您可以測試後，使用多個執行個體和服務的複本。
1 的節點叢集模式已最佳化而適合做快速部署及註冊服務，可協助您快速驗證使用的服務布料的轉印圖樣執行階段的程式碼。

1 的節點叢集模式和 5 的節點叢集模式不是模擬器。 執行相同的平台程式碼的多機器叢集上找到。

> [AZURE.NOTE] 此功能可在 SDK 5.2 版。

若要變更的 1 的節點叢集叢集模式，您可以使用服務布料的轉印圖樣本機叢集管理員，或使用 PowerShell 以下列方式︰

1. 以系統管理員身分啟動新的 PowerShell 視窗。

2. 從 SDK 資料夾執行叢集安裝指令碼︰

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    叢集安裝需要一些時間。 安裝完成後，您應該會看到類似的輸出︰
    
    ![叢集設定輸出][cluster-setup-success-1-node]

如果您使用的服務布料的轉印圖樣本機叢集管理員︰

![切換叢集模式][switch-cluster-mode]

> [AZURE.WARNING] 在變更叢集模式，請從您的系統移除目前的叢集，然後建立新的叢集。 當您變更叢集模式時，會刪除中叢集，您必須儲存的資料。

## <a name="next-steps"></a>後續步驟
- 現在您已經部署，升級的一些預先建立的應用程式，您可以[嘗試建立您自己在 Visual Studio 中](service-fabric-create-your-first-application-in-visual-studio.md)。
- [Azure 叢集](service-fabric-cluster-creation-via-portal.md)，也可以執行所有本文中的本機叢集上都執行的動作。
- 升級我們本文中所執行的是基本的。 請參閱[升級文件](service-fabric-application-upgrade.md)若要進一步瞭解的功能和彈性的服務布料的轉印圖樣升級。

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
