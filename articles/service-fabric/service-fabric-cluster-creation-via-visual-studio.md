<properties
   pageTitle="設定使用 Visual Studio 服務布料的轉印圖樣叢集 |Microsoft Azure"
   description="說明如何使用 Azure 資源管理員範本建立的 Visual Studio 的 Azure 資源群組專案設定服務布料的轉印圖樣叢集"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>使用 Visual Studio 設定服務布料的轉印圖樣叢集
本文將說明如何設定 Azure 服務布料的轉印圖樣叢集使用 Visual Studio 與 Azure 資源管理員範本。 若要建立範本，我們將使用 Visual Studio Azure 資源群組專案。 已經建立範本後，可以部署至 Azure 直接從 Visual Studio。 此外，它也可以用指令碼，從或連續整合 (SONT) 設備一部分。

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>建立使用 Azure 資源群組專案的服務布料的轉印圖樣叢集範本
若要開始，請開啟 Visual Studio 並建立 Azure 資源群組專案 （它位於**雲端**資料夾）︰

![選取 Azure 資源群組專案的新專案] 對話方塊][1]

您可以建立新的 Visual Studio 方案為此專案，或將其新增至現有的方案。

>[AZURE.NOTE] 如果您沒有看到 Azure 資源群組專案的雲端節點下，表示您沒有安裝 Azure SDK。 啟動 Web 平台安裝程式 （[立即安裝](http://www.microsoft.com/web/downloads/platform.aspx)如果您還未），然後在搜尋 「 Azure.NET SDK 」 和安裝的版本，與您的 Visual Studio 版本相容。

按 [確定] 按鈕後，Visual Studio 會要求您選取您想要建立的資源管理員範本︰

![選取所選的服務布料的轉印圖樣叢集範本 Azure 範本] 對話方塊][2]

選取 [服務布料的轉印圖樣叢集範本，然後再按 [確定] 按鈕。 現在已建立專案和資源管理員範本。

## <a name="prepare-the-template-for-deployment"></a>準備範本以供部署
若要建立叢集部署範本之前，您必須提供值所需的範本參數的。 下列參數值都從讀取`ServiceFabricCluster.parameters.json`檔案，這是`Templates`資源群組專案的資料夾。 開啟檔案，並提供下列的值︰

|參數名稱           |描述|
|-----------------------  |--------------------------|
|adminUserName            |服務布料的轉印圖樣機器 （節點） 系統管理員帳戶的名稱。|
|certificateThumbprint    |憑證的指紋保護叢集的憑證。|
|sourceVaultResourceId    |儲存憑證保護叢集金鑰保存庫*資源識別碼*。|
|certificateUrlValue      |叢集安全性憑證的 URL。|

Visual Studio 服務布料的轉印圖樣資源管理員範本會建立安全叢集所保護的憑證。 這個憑證由最後三個樣板參數 (`certificateThumbprint`， `sourceVaultValue`，及`certificateUrlValue`)，且必須位於**Azure 金鑰保存庫**。 如需有關如何建立叢集安全性憑證，請參閱[服務布料的轉印圖樣叢集安全性案例](service-fabric-cluster-security.md#x509-certificates-and-service-fabric)的文章。

## <a name="optional-change-the-cluster-name"></a>可省略︰ 變更叢集名稱
每個服務布料的轉印圖樣叢集有一個名稱。 Azure 中建立結構叢集時，叢集名稱決定叢集的網域名稱系統 (DNS) 名稱 （連同 Azure 區域中）。 例如，如果您命名叢集`myBigCluster`，將會裝載新叢集資源群組的位置 （Azure 區域） 是東亞美國，叢集 DNS 名稱會`myBigCluster.eastus.cloudapp.azure.com`。

預設叢集名稱會自動產生，並會重覆隨機尾碼附加到 「 叢集 「 前置字元。 如此可讓您很容易使用範本為**連續整合**(SONT) 系統的一部分。 如果您想要使用的特定的名稱，為您的叢集，一個有意義的設定值的`clusterName`變數資源管理員範本檔案 (`ServiceFabricCluster.json`) 至您所選的名稱。 則定義該檔案中的第一個變數。

## <a name="optional-add-public-application-ports"></a>可省略︰ 新增公用應用程式的連接埠
您也可能會想要變更公用應用程式的連接埠叢集，然後將其部署。 根據預設，範本開啟兩個公用 TCP 連接埠 （80 和 8081）。 如果您需要更多應用程式，請修改 Azure 負載平衡器定義在範本中。 定義會儲存在主要的範本檔案 (`ServiceFabricCluster.json`)。 開啟該檔案，然後搜尋`loadBalancedAppPort`。 每個連接埠是三個成品與相關聯的︰

1. 範本變數定義 TCP 連接埠值的連接埠︰

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. *探查*定義頻率以及多久 Azure 負載平衡器會嘗試使用容錯移轉至另一個之前的特定服務布料的轉印圖樣節點。 探查是負載平衡器資源的一部分。 以下是探查定義的第一個的預設應用程式連接埠︰

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. *負載平衡規則*的緊密整合探查，這可讓負載平衡服務布料的轉印圖樣叢集節點的一組和連接埠︰

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
如果您打算要部署至此叢集的應用程式需要更多的連接埠，您可以將其新增來建立其他探查和負載平衡規則定義。 如需有關如何使用 Azure 負載平衡器 」，透過資源管理員範本的詳細資訊，請參閱[開始使用範本內部負載平衡器的建立](../load-balancer/load-balancer-get-started-ilb-arm-template.md)。

## <a name="deploy-the-template-by-using-visual-studio"></a>使用 Visual Studio 中部署範本
在您儲存中的所有必要的參數值後`ServiceFabricCluster.param.dev.json`檔案，您已準備好部署範本，並建立您的服務布料的轉印圖樣叢集。 資源群組專案 Visual Studio 方案總管] 中的以滑鼠右鍵按一下，然後選擇 [**部署 |新的部署...**。 必要時，Visual Studio 會顯示 [**部署至 [資源群組**] 對話方塊中，要求您驗證 Azure:

![部署至 [資源群組] 對話方塊][3]

對話方塊可讓您選擇現有的資源管理員資源群組叢集，並讓您建立一個新的選項。 以正常方式讓服務布料的轉印圖樣叢集使用不同的資源群組。

按下 [部署] 按鈕後，Visual Studio 會提示您確認範本參數值。 按下 [**儲存**] 按鈕。 一個參數沒有持續的值︰ 叢集的系統管理帳戶密碼。 您需要 Visual Studio 會提示您輸入一個時提供密碼值。

>[AZURE.NOTE] Visual Studio Azure SDK 2.9 開始著手，部署期間支援從**Azure 金鑰保存庫**讀取密碼。 在 [範本參數] 對話方塊中，請注意`adminPassword`參數文字方塊右側具有小的 「 金鑰 」 圖示。 此圖示可讓您選取現有的按鍵保存庫私人叢集的系統管理員的密碼。 只需確定至第一個啟用您的主要保存庫中進階存取原則範本部署 Azure 資源管理員存取權。 

您可以監視部署程序，在 Visual Studio 輸出視窗中的進度。 一旦完成範本部署，您的新叢集已可供使用 ！

>[AZURE.NOTE] 如果 PowerShell 永遠不會用來管理 Azure 從現在，您所使用的電腦，您需要執行一些內部。
>1. 啟用 PowerShell 指令碼執行[`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx)] 命令。 開發機器 」 不受限制的 「 原則是通常可接受的。
>2. 決定是否要讓診斷資料收集 Azure PowerShell 命令，並執行[`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx)或[`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx)視。 這會在範本部署避免不必要的提示。

如果有任何錯誤，請移至[Azure 入口網站](https://portal.azure.com/)，並開啟您要部署資源群組。 按一下 [**所有設定**]，然後按一下 [設定刀中的 [**部署**。 失敗的資源群組部署那里保留診斷的詳細的資訊。

>[AZURE.NOTE] 服務布料的轉印圖樣叢集需要設定，若要維持可用性並保留狀態-稱為 「 維護仲裁。 」 的節點數目 因此，不安全關閉所有叢集機器，除非您已先執行[完整備份您的狀態](service-fabric-reliable-services-backup-restore.md)。

## <a name="next-steps"></a>後續步驟
- [進一步瞭解如何設定服務布料的轉印圖樣叢集使用 Azure 入口網站](service-fabric-cluster-creation-via-portal.md)
- [瞭解如何管理及部署使用 Visual Studio 服務布料的轉印圖樣應用程式](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
