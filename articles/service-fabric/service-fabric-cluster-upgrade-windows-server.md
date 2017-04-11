<properties
   pageTitle="升級在 Windows Server 上的獨立版服務布料的轉印圖樣叢集 |Microsoft Azure"
   description="升級的程式碼服務布料的轉印圖樣及/或執行獨立版服務布料的轉印圖樣叢集，包括設定叢集更新模式的設定"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>升級您在 Windows Server 上的獨立版服務布料的轉印圖樣叢集

> [AZURE.SELECTOR]
- [Azure 叢集](service-fabric-cluster-upgrade.md)
- [獨立叢集](service-fabric-cluster-upgrade-windows-server.md)

設計 upgradability 的任何新的系統是產品的達成長期成功，您金鑰。 服務布料的轉印圖樣叢集是您擁有自己的資源。 本文將說明如何您可以確保叢集一律執行服務布料的轉印圖樣的受支援的版本的程式碼和設定。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>控制叢集執行布料的轉印圖樣版本

您可以設定叢集 Microsoft 發行新版本時，請下載服務布料的轉印圖樣更新，或選擇 [選取您想要在叢集支援布料的轉印圖樣版本。 

您執行此動作 」 fabricClusterAutoupgradeEnabled 」 叢集設定設為 true 或 false。


>[AZURE.NOTE] 請確定保留叢集一律執行的是受支援的服務布料的轉印圖樣版本。 時，我們宣告服務布料的轉印圖樣的新版本的版本時，先前的版本已標示的支援結束後的 60 天的日期從最小值。 新的版本是公告[服務布料的轉印圖樣小組部落格上](https://blogs.msdn.microsoft.com/azureservicefabric/ )。 使用，然後選擇新的版本。 


只有當您使用的實際執行式節點的設定，每個服務布料的轉印圖樣節點的配置獨立的實體或虛擬機器上的位置，您可以叢集升級至新的版本。 如果您有開發叢集，有一個以上的服務布料的轉印圖樣節點單一的實體或虛擬機器上，您必須叢集清除並重新建立新的版本。


有兩個不同的工作流程至最新的或支援的服務布料的轉印圖樣版本升級叢集。 若要自動下載最新版本的連線的叢集以及第二個叢集沒有連線至下載最新版的服務布料的轉印圖樣的。

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>若要下載最新的程式碼和設定連線升級叢集 

使用這些步驟將叢集升級至受支援的版本，如果叢集節點有網際網路連線至[http://download.microsoft.com](http://download.microsoft.com) 

針對已連線至[http://download.microsoft.com](http://download.microsoft.com)的叢集，我們定期檢查有可用的服務布料的轉印圖樣的新版本。


當有新的服務布料的轉印圖樣版本時，套件下載至本機叢集，而佈建升級。 此外，通知這個新版本的客戶，系統就會有明確叢集狀況警告類似下列︰

「 目前叢集版本 [版本 #] 結束支援的 [日期]。 」 後叢集正在執行最新版本，, 警告消失。


#### <a name="cluster-upgrade-workflow"></a>叢集升級的工作流程。
 
當您看到叢集狀況警告時，您需要執行下列動作︰

1. 從任何已列為叢集節點的所有電腦的系統管理員存取權的電腦連線到叢集。 電腦上執行這個指令碼沒有叢集的一部分

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. 取得服務布料的轉印圖樣的清單，您可以升級至的版本

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    您應該取得輸出如下所示︰

    ![取得布料的轉印圖樣版本][getfabversions]

3. 啟動其中一個版本可供使用[開始 ServiceFabricClusterUpgrade PowerShell cmd](https://msdn.microsoft.com/library/mt125872.aspx)叢集升級

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
您可以監視服務布料的轉印圖樣總管或藉由執行下列 power shell 指令升級的進度

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

一旦您已修正導致復原的問題，您必須按照與之前相同的步驟，再次啟動升級。


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>升級具有叢集<U>沒有連線</u>下載最新的程式碼和設定

使用這些步驟將叢集升級至受支援的版本，如果您叢集節點**沒有**網際網路連線至[http://download.microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]如果您執行的不是網際網路連線的叢集，則必須監視服務布料的轉印圖樣小組部落格，以取得新的版本的通知。 系統**不會**置於要通知您的任何叢集狀況警告。  

1. 修改您叢集設定下列屬性設定為 false。

        "fabricClusterAutoupgradeEnabled": false,

然後，啟動設定升級。 如需使用方式的詳細資訊，請參閱[開始 ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) 。 叢集顯示版本是您在 clusterConfig.JSON 的版本。 請務必更新之前關閉設定升級預定的時間。

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>叢集升級的工作流程。
 


1. 下載套件的最新版本[的 windows server 建立服務布料的轉印圖樣叢集](service-fabric-cluster-creation-for-windows-server.md)文件 


1. 從任何已列為叢集節點的所有電腦的系統管理員存取權的電腦連線到叢集。 電腦上執行這個指令碼沒有叢集的一部分 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. 複製下載的套件叢集圖像存放區。

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. 註冊複製的套件 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. 啟動叢集升級至其中一個可用的版本。 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
您可以監視服務布料的轉印圖樣總管或藉由執行下列 power shell 指令升級的進度

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

一旦您已修正導致復原的問題，您必須按照與之前相同的步驟，再次啟動升級。



## <a name="next-steps"></a>後續步驟
- 瞭解如何自訂部分[服務布料的轉印圖樣叢集布料的轉印圖樣設定](service-fabric-cluster-fabric-settings.md)
- 瞭解如何[調整叢集和縮小](service-fabric-cluster-scale-up-down.md)
- 深入瞭解[應用程式升級](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
