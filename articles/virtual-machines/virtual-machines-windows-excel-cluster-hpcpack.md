<properties
 pageTitle="適用於 Excel 和 SOA HPC 套件叢集 |Microsoft Azure"
 description="開始執行 Azure 中 HPC 套件叢集上的大型 Excel 和 SOA 負載"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/25/2016"
 ms.author="danlep"/>

# <a name="get-started-running-excel-and-soa-workloads-on-an-hpc-pack-cluster-in-azure"></a>開始執行 Azure 中 HPC 套件叢集上的 Excel 和 SOA 負載

本文將示範如何使用 Azure 快速入門範本，或您也可以選擇 PowerShell 的 Azure 部署指令碼部署 Microsoft HPC 套件叢集 Azure 虛擬機器上。 叢集使用 Azure Marketplace VM 設計 HPC pack 執行 Microsoft Excel 或服務導向架構 (SOA) 工作負載的圖像。 您可以使用叢集從內部部署的用戶端電腦上執行簡單的 Excel HPC 和 SOA 服務。 Excel HPC 服務包含 Excel 活頁簿卸載和 Excel 使用者定義函數或 Udf。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

在高的層級下, 圖顯示 HPC 套件叢集您所建立。

![執行的 Excel 工作負載的節點的 HPC 叢集][scenario]

## <a name="prerequisites"></a>必要條件

*   **用戶端電腦**-您需要 Windows 型的用戶端電腦來提交到叢集範例 Excel 和 SOA 工作。 您也需要 Windows 電腦執行的 PowerShell 的 Azure 叢集部署指令碼 （如果您選擇的部署方法） 和

*   **Azure 訂閱**-如果您沒有 Azure 的訂閱，您可以建立的[免費帳戶](https://azure.microsoft.com/pricing/free-trial/)在幾分鐘。

*   **核心配額**-您可能需要增加的配額的核心，尤其是如果您部署與多核心 VM 大小的數個叢集節點。 如果您使用的 Azure 快速入門範本，核心配額資源管理員] 中是每個 Azure 地區。 在此情況下，您可能需要增加的配額中的特定區域。 請參閱[Azure 訂閱限制、 配額和限制式](../azure-subscription-service-limits.md)。 若要增加配額] 中，[開啟線上客戶支援要求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)免費。

*   已安裝**Microsoft Office 授權**-如果您部署運算節點 Microsoft Excel 中，30 天試用版的 Microsoft Excel 專業增強版 2013年搭配使用的服務商場 HPC 套件 VM 圖像。 之後試用期，您需要提供啟動 Excel，繼續執行負載有效的 Microsoft Office 授權。 本文稍後的請參閱[Excel 啟動](#excel-activation)。 


## <a name="step-1-set-up-an-hpc-pack-cluster-in-azure"></a>步驟 1。 設定 HPC 套件中叢集 Azure

我們會顯示兩個選項可以設定叢集︰ 首先，使用 Azure 快速入門範本和 Azure 入口網站。與第二個，使用 PowerShell 的 Azure 部署指令碼。


### <a name="option-1-use-a-quickstart-template"></a>選項 1。 使用範本快速入門
若要快速且輕鬆地部署 HPC 套件中的叢集 Azure 入口網站中使用 Azure 快速入門範本。 當您開啟範本，在入口網站時，您會收到的簡單的 UI，供您輸入設定叢集。 以下是步驟。 

>[AZURE.TIP]如果您想使用[Azure Marketplace 範本](https://portal.azure.com/?feature.relex=*%2CHubsExtension#create/microsofthpc.newclusterexcelcn)建立類似叢集專為 Excel 工作負載的。 步驟稍有不同的動作。

1.  請造訪[GitHub 建立 HPC 叢集範本頁面](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)。 如果您想，檢閱範本與程式碼的相關資訊。

2.  按一下 [**部署至 Azure** Azure 入口網站中的範本開始進行部署]。

    ![部署至 Azure 範本][github]

3.  在入口網站，請遵循下列步驟以輸入參數 HPC 叢集範本。

    。 在 [**參數**] 頁面中，輸入或修改的範本參數值。 （按一下說明資訊的每個設定旁的圖示）。範例值是以下列畫面所示。 此範例會建立名為*hpc01* *hpc.local*網域所組成的標頭的節點中叢集，2 運算節點。 建立運算節點 HPC 套件 VM 圖像，其中包含 Microsoft Excel 中。

    ![輸入參數][parameters]

    >[AZURE.NOTE]主節點 VM 會自動建立從[最新的服務商場圖像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)的 Windows Server 2012 R2 上 HPC 套件 2012 R2。 目前圖像根據 HPC 套件 2012 R2 更新 3。
    >
    >計算節點 Vm 建立從所選的計算節點系列的最新的圖像。 選取包含的試用版的 Microsoft Excel Professional Plus 2013 最新 HPC 套件計算節點影像**ComputeNodeWithExcel**選項。 若要部署叢集一般 SOA 工作階段或 Excel UDF 卸載選擇**ComputeNode**選項 （沒有安裝的 Excel)。

    b。 選擇 [訂閱]。

    c。 建立叢集，例如*hpc01RG*資源群組。

    d。 選擇 [資源] 群組中，例如美國中部的位置。

    e。 在 [**法律條款**] 頁面上檢閱條款。 如果您同意的話，請按一下 [**購買**]。 然後，當您完成設定範本中的值時，按一下 [**建立**。

4.  部署完成時 （通常是花大約 30 分鐘），叢集憑證檔案匯出叢集主節點。 接下來的步驟，在您匯入這個公用憑證提供安全 HTTP 繫結的伺服器端驗證用戶端電腦上。

    。 從 Azure 入口網站，以連線至主節點的遠端桌面。

     ![連線至主節點][connect]

    b。 使用憑證管理員 」 中的標準程序匯出主節點憑證 （位於憑證︰ \LocalMachine\My） 沒有私密金鑰。 在此範例中，匯出*CN = hpc01.eastus.cloudapp.azure.com*。

    ![匯出憑證][cert]

### <a name="option-2-use-the-hpc-pack-iaas-deployment-script"></a>選項 2。 使用 HPC 套件 IaaS 部署指令碼

HPC 套件 IaaS 部署指令碼提供另一個多功能部署 HPC 套件叢集的方法。 在傳統的部署模型中,，而範本是使用 Azure 資源管理員部署模型，它會建立叢集。 此外，指令碼是相容於 Azure 全域管理員或 Azure china （中國） 服務中的訂閱。

**其他的先決條件**

* **Azure PowerShell** - [安裝及設定 PowerShell 的 Azure](../powershell-install-configure.md) (版本 0.8.10 或更新版本) 用戶端電腦上。

* **HPC 套件 IaaS 部署指令碼**-下載並解壓縮從[Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=44949)的指令碼的最新版本。 執行檢查的指令碼版本`New-HPCIaaSCluster.ps1 –Version`。 本文根據版本 4.5.0 或更新版本的指令碼。

**建立設定檔**

 HPC 套件 IaaS 部署指令碼會使用 XML 設定檔，做為輸入描述 HPC 叢集的基礎結構。 若要部署包含標頭的節點和從運算節點圖像，其中包含 Microsoft Excel 中建立的 18 運算節點叢集替代環境將下列範例設定檔的值。 如需設定檔的詳細資訊，請參閱在指令碼] 資料夾和[建立 HPC 套件 IaaS 部署指令碼 HPC 叢集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)Manual.rtf 檔案。

```
<?xml version="1.0" encoding="utf-8"?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>MySubscription</SubscriptionName>
    <StorageAccount>hpc01</StorageAccount>
  </Subscription>
  <Location>West US</Location>
  <VNet>
    <VNetName>hpc-vnet01</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>HPCExcelDC01</VMName>
      <ServiceName>HPCExcelDC01</ServiceName>
      <VMSize>Medium</VMSize>
    </DomainController>
  </Domain>
   <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>HPCExcelHN01</VMName>
    <ServiceName>HPCExcelHN01</ServiceName>
    <VMSize>Large</VMSize>
    <EnableRESTAPI/>
    <EnableWebPortal/>
    <PostConfigScript>C:\tests\PostConfig.ps1</PostConfigScript>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>HPCExcelCN%00%</VMNamePattern>
    <ServiceName>HPCExcelCN01</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>18</NodeCount>
    <ImageName>HPCPack2012R2_ComputeNodeWithExcel</ImageName>
  </ComputeNodes>
</IaaSClusterConfig>
```

**設定檔的相關附註**

* **必須**的主節點**VMName** **ServiceName**，相同或 SOA 工作無法執行。

* 請確定您指定**EnableWebPortal**以便標頭節點憑證產生並匯出。

* 檔案指定後設定 PowerShell 指令碼 PostConfig.ps1 在主節點上執行。 下列範例指令碼設定 Azure 儲存連線字串、 主節點，會移除計算節點角色及部署時，會將所有節點線上。 

```
    # add the HPC Pack powershell cmdlets
        Add-PSSnapin Microsoft.HPC

    # set the Azure storage connection string for the cluster
        Set-HpcClusterProperty -AzureStorageConnectionString 'DefaultEndpointsProtocol=https;AccountName=<yourstorageaccountname>;AccountKey=<yourstorageaccountkey>'

    # remove the compute node role for head node to make sure the Excel workbook won’t run on head node
        Get-HpcNode -GroupName HeadNodes | Set-HpcNodeState -State offline | Set-HpcNode -Role BrokerNode

    # total number of nodes in the deployment including the head node and compute nodes, which should match the number specified in the XML configuration file
        $TotalNumOfNodes = 19

        $ErrorActionPreference = 'SilentlyContinue'

    # bring nodes online when they are deployed until all nodes are online
        while ($true)
        {
          Get-HpcNode -State Offline | Set-HpcNodeState -State Online -Confirm:$false
          $OnlineNodes = @(Get-HpcNode -State Online)
          if ($OnlineNodes.Count -eq $TotalNumOfNodes)
          {
             break
          }
          sleep 60
        }
```

**執行指令碼**

1.  以管理員身分開啟 PowerShell 主控台用戶端電腦上。

2.  變更目錄的指令碼資料夾 (在此範例中 E:\IaaSClusterScript)。

    ```
    cd E:\IaaSClusterScript
    ```
    
3.  若要部署 HPC 套件叢集，執行下列命令。 假設設定檔案位於 E:\HPCDemoConfig.xml。

    ```
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```

HPC 套件部署指令碼執行的一些時間。 指令碼會執行的項目是匯出並下載叢集憑證，然後將其儲存為目前使用者的文件] 資料夾中，用戶端電腦上。 指令碼會產生類似下列訊息。 在下列步驟中，您匯入的適當的憑證存放區中的憑證。    
    
    You have enabled REST API or web portal on HPC Pack head node. Please import the following certificate in the Trusted Root Certification Authorities certificate store on the computer where you are submitting job or accessing the HPC web portal:
    C:\Users\hpcuser\Documents\HPCWebComponent_HPCExcelHN004_20150707162011.cer

## <a name="step-2-offload-excel-workbooks-and-run-udfs-from-an-on-premises-client"></a>步驟 2。 卸載 Excel 活頁簿，並從內部部署用戶端執行 Udf

### <a name="excel-activation"></a>Excel 啟動

使用生產負載 ComputeNodeWithExcel VM 圖像，當您需要提供正確的 Microsoft Office 授權索引鍵，在 [運算節點上啟動 Excel。 否則，試用版的 Excel 到期之後 30 天，並執行的 Excel 活頁簿會失敗，COMException (0x800AC472)。 

您可以重新 Excel 啟用的評估時間的另一個 30 天︰ 登入的標頭節點及 clusrun`%ProgramFiles(x86)%\Microsoft Office\Office15\OSPPREARM.exe`上所有的 Excel 計算節點透過 HPC 叢集管理員。 您可以重新啟用兩次最大值。 之後，您必須提供有效的 Office 授權金鑰。

Office Professional Plus 2013 安裝 VM 圖像是大量版本與一般大量授權鍵 (GVLK)。 您可以透過金鑰管理服務 (KMS) 啟動它 / Active Directory-Based 啟動 (AD BA) 或多重啟動金鑰 (MAK)。 

    * 若要使用 KMS/AD-BA，使用現有的 KMS 伺服器或設定一個新使用 Microsoft Office 2013 大量授權套件。 （如果您要設定主節點的伺服器。）然後，啟動 KMS 主機金鑰，透過網際網路或電話。 然後 clusrun`ospp.vbs`設定 KMS 伺服器和連接埠和所有上啟動 Office Excel 運算節點。 
    
    * 若要使用 MAK，第一個 clusrun`ospp.vbs`輸入鍵，然後啟動所有 Excel 運算透過網際網路或電話的節點。 

>[AZURE.NOTE]零售產品金鑰的 Office Professional Plus 2013 無法使用此 VM 圖像。 如果您擁有有效的索引鍵和非此 Office Professional Plus 2013 大量版本的 Office 或 Excel 版本的安裝媒體，您可以改為使用。 請先解除安裝此大量版本，然後安裝您的版本。 重新安裝的 Excel 運算節點可以擷取為使用在部署中的自訂 VM 圖像。

### <a name="offload-excel-workbooks"></a>卸載 Excel 活頁簿

遵循下列步驟，讓它執行 HPC 套件叢集 Azure 中卸載 Excel 活頁簿。 若要這麼做，您必須 Excel 2010 或 2013 用戶端電腦上已安裝。

1. 使用其中一個選項在 [步驟 1 中部署與 Excel HPC 套件叢集計算節點圖像。 取得叢集憑證檔案 (.cer) 以及叢集使用者名稱和密碼。

2. 用戶端電腦上，匯入憑證︰ \CurrentUser\Root 叢集憑證。

3. 請確定已安裝 Excel。 建立 [Excel.exe 用戶端電腦上的相同資料夾中的下列內容 Excel.exe.config 檔案。 此步驟可確保的 HPC 套件 2012 R2 Excel COM 增益集成功載入。

    ```
    <?xml version="1.0"?>
    <configuration>
        <startup useLegacyV2RuntimeActivationPolicy="true">
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.0"/>
        </startup>
    </configuration>
    ```
    
4.  設定用戶端來提交到 HPC 套件叢集的工作。 其中一個選項是要下載完整[HPC 套件 2012 R2 更新 3 安裝](http://www.microsoft.com/download/details.aspx?id=49922)和安裝 HPC 套件用戶端。 或者，下載並安裝[HPC 套件 2012 R2 更新 3 用戶端工具](https://www.microsoft.com/download/details.aspx?id=49923)和適當 Visual c + + 2010 可轉散發套件為您的電腦 （[x64](http://www.microsoft.com/download/details.aspx?id=14632)、 [x86](https://www.microsoft.com/download/details.aspx?id=5555)）。

5.  在此範例中，我們使用名為 ConvertiblePricing_Complete.xlsb 的 Excel 活頁簿範例。 您可以下載[以下](https://www.microsoft.com/en-us/download/details.aspx?id=2939)。

6.  將 Excel 活頁簿複製到 [工作] 資料夾，例如 D:\Excel\Run。

7.  開啟 Excel 活頁簿。 **開發**功能區上按一下 [ **COM 增益集]** ，然後 HPC 套件 Excel COM 增益集已載入成功。

    ![Excel 增益集 HPC 套件][addin]

8.  編輯 VBA 巨集在 Excel 中 HPCControlMacros 變更解行下列指令碼所示。 取代您的環境適當的值。

    ![Excel 巨集 HPC 套件][macro]

    ```
    'Private Const HPC_ClusterScheduler = "HEADNODE_NAME"
    Private Const HPC_ClusterScheduler = "hpc01.eastus.cloudapp.azure.com"

    'Private Const HPC_NetworkShare = "\\PATH\TO\SHARE\DIRECTORY"
    Private Const HPC_DependFiles = "D:\Excel\Upload\ConvertiblePricing_Complete.xlsb=ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.Initialize ActiveWorkbook
    HPCExcelClient.Initialize ActiveWorkbook, HPC_DependFiles

    'HPCWorkbookPath = HPC_NetworkShare & Application.PathSeparator & ActiveWorkbook.name
    HPCWorkbookPath = "ConvertiblePricing_Complete.xlsb"

    'HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath
    HPCExcelClient.OpenSession headNode:=HPC_ClusterScheduler, remoteWorkbookPath:=HPCWorkbookPath, UserName:="hpc\azureuser", Password:="<YourPassword>"
```

9.  將複製的 Excel 活頁簿上傳目錄 D:\Excel\Upload 等。 在 [VBA 巨集 HPC_DependsFiles 常數中指定此目錄。

10. 若要執行叢集 Azure 中的活頁簿，請按一下工作表上的 [**叢集**] 按鈕。

### <a name="run-excel-udfs"></a>執行 Excel Udf

若要執行 Excel Udf，請遵循上述步驟 1-3，將用戶端電腦設定。 Excel Udf 的您不需要有 Excel 應用程式安裝在運算節點。 因此時建立叢集計算節點，, 您可以選擇標準計算節點影像，而不是使用 Excel 計算節點圖像。

>[AZURE.NOTE] 有 34 字元的限制在 Excel 2010 及 2013年叢集連接器] 對話方塊。 您可以使用這個對話方塊來指定執行 Udf 叢集。 如果完整叢集名稱長度 (例如，hpcexcelhn01.southeastasia.cloudapp.azure.com)，無法容納在對話方塊中。 因應措施若要設定全機器變數*CCP_IAASHN*例如長叢集名稱的值。 為叢集主節點名稱，然後在對話方塊中輸入*%ccp_iaashn%* 。 

成功部署叢集後，繼續執行下列步驟，以執行內建樣本 Excel UDF。 自訂 Excel Udf，請參閱建立 Xll 及部署 IaaS 叢集這些[資源](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)。

1.  開啟新的 Excel 活頁簿。 **開發**功能區上按一下 [**增益集**。 然後，在對話方塊中，按一下 [**瀏覽]**，請瀏覽至 [%CCP_HOME%Bin\XLL32] 資料夾，然後選取範例 ClusterUDF32.xll。 如果在用戶端電腦上沒有 ClusterUDF32，請將其複製的 %CCP_HOME%Bin\XLL32 資料夾的標頭的節點。

    ![選取 UDF][udf]

2.  按一下 [**檔案** > **選項** > **進階**。 在 [**公式**] 底下，核取 [**允許使用者定義 XLL 的函數來執行叢集**。 然後按一下 [**選項]** ，並輸入完整的叢集名稱**叢集主節點**名稱。 （註明先前此輸入的方塊是 34 字元，可能不符合長叢集名稱。 您可能會使用全機器變數長叢集名稱）。

    ![設定 UDF][options]

3.  若要執行 UDF 計算叢集上，按一下 [值 =XllGetComputerNameC() 的儲存格，然後按 Enter。 此函數只擷取 UDF 執行運算節點的名稱。 第一次執行，認證] 對話方塊會提示使用者名稱和密碼連線至 IaaS 叢集。

    ![執行 UDF][run]

    若要計算多個儲存格時，按下 ctrl Shift Alt 鍵 + F9，所有的儲存格上執行計算。

## <a name="step-3-run-a-soa-workload-from-an-on-premises-client"></a>步驟 3。 從內部部署用戶端執行 SOA 工作量

若要執行一般 SOA 應用程式 HPC 套件 IaaS 叢集上，先使用其中一個方法在步驟 1 中部署叢集。 指定一般計算節點影像在此情況下，因為您不需要在運算節點 Excel。 然後遵循下列步驟。

1. 後擷取叢集憑證，請將它匯入憑證︰ \CurrentUser\Root 在用戶端電腦。

2. 安裝[HPC 套件 2012 R2 更新 3 SDK](http://www.microsoft.com/download/details.aspx?id=49921)及[HPC 套件 2012 R2 更新 3 用戶端工具](https://www.microsoft.com/download/details.aspx?id=49923)。 這些工具可讓您開發並執行 SOA 用戶端應用程式。

3. 下載 HelloWorldR2[範例程式碼](https://www.microsoft.com/download/details.aspx?id=41633)。 在 Visual Studio 2010 或 2012年中開啟 HelloWorldR2.sln。

4. 第一次建立 EchoService 專案。 以相同的方式將部署至內部部署叢集，然後 IaaS 叢集部署服務。 如需詳細步驟，請參閱在 HelloWordR2 Readme.doc。 修改，並建立 HellWorldR2 和其他專案中下一節所述產生 Azure IaaS 叢集執行 SOA 用戶端應用程式。

### <a name="use-http-binding-with-azure-storage-queue"></a>Http 繫結使用 Azure 儲存佇列中

若要使用 Http 繫結 Azure 儲存佇列中，變更幾個範例程式碼。

* 更新叢集名稱。

    ```
// Before
const string headnode = "[headnode]";
// After e.g.
const string headnode = "hpc01.eastus.cloudapp.azure.com";
or
const string headnode = "hpc01.cloudapp.net";
```

* 您也可以使用預設 TransportScheme 中 SessionStartInfo 或明確地將其設定為 Http。

```
    info.TransportScheme = TransportScheme.Http;
```

* 使用預設繫結 BrokerClient。

    ```
// Before
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session, binding))
// After
using (BrokerClient<IService1> client = new BrokerClient<IService1>(session))
```

    或使用 basicHttpBinding。

    ```
BasicHttpBinding binding = new BasicHttpBinding(BasicHttpSecurityMode.TransportWithMessageCredential);
binding.Security.Message.ClientCredentialType = BasicHttpMessageCredentialType.UserName;    binding.Security.Transport.ClientCredentialType = HttpClientCredentialType.None;
```

* 您也可以為 true SessionStartInfo 中設定 UseAzureQueue 旗標。 如果未設定，它將會設定為預設，當叢集名稱具有 Azure 網域尾碼，並且 TransportScheme Http 時，則為 true。

    ```
    info.UseAzureQueue = true;
```

###<a name="use-http-binding-without-azure-storage-queue"></a>使用 Http 繫結不 Azure 儲存佇列中

若要使用 Http 繫結不 Azure 儲存佇列中，為 false SessionStartInfo 中明確設定 UseAzureQueue 標幟。

```
    info.UseAzureQueue = false;
```

### <a name="use-nettcp-binding"></a>使用 NetTcp 繫結

若要使用 NetTcp 繫結，設定不類似連接到內部部署叢集。 您需要開啟主節點 VM 的幾個端點。 如果您是使用 HPC 套件 IaaS 部署指令碼建立叢集，例如設定端點 Azure 傳統入口網站中，如下所示。


1. 停止 VM。

2. 新增 TCP 連接埠 9090，9087，9091，9094 工作階段，仲介處理、 仲介分別處理工作人員，以及資料服務

    ![設定結束點][endpoint]

3. 啟動 VM。

SOA 用戶端應用程式不需要變更除了變更 IaaS 叢集全名的標頭名稱。

## <a name="next-steps"></a>後續步驟

* 請參閱[下列資源](http://social.technet.microsoft.com/wiki/contents/articles/1198.windows-hpc-and-microsoft-excel-resources-for-building-cluster-ready-workbooks.aspx)的 HPC pack 執行 Excel 工作負載的相關詳細資訊。

* 有關部署及管理 HPC pack SOA 服務的詳細資訊，請參閱[管理 Microsoft HPC 套件的 SOA 服務](https://technet.microsoft.com/library/ff919412.aspx)。

<!--Image references-->
[scenario]: ./media/virtual-machines-windows-excel-cluster-hpcpack/scenario.png
[github]: ./media/virtual-machines-windows-excel-cluster-hpcpack/github.png
[template]: ./media/virtual-machines-windows-excel-cluster-hpcpack/template.png
[parameters]: ./media/virtual-machines-windows-excel-cluster-hpcpack/parameters.png
[create]: ./media/virtual-machines-windows-excel-cluster-hpcpack/create.png
[connect]: ./media/virtual-machines-windows-excel-cluster-hpcpack/connect.png
[cert]: ./media/virtual-machines-windows-excel-cluster-hpcpack/cert.png
[addin]: ./media/virtual-machines-windows-excel-cluster-hpcpack/addin.png
[macro]: ./media/virtual-machines-windows-excel-cluster-hpcpack/macro.png
[options]: ./media/virtual-machines-windows-excel-cluster-hpcpack/options.png
[run]: ./media/virtual-machines-windows-excel-cluster-hpcpack/run.png
[endpoint]: ./media/virtual-machines-windows-excel-cluster-hpcpack/endpoint.png
[udf]: ./media/virtual-machines-windows-excel-cluster-hpcpack/udf.png
