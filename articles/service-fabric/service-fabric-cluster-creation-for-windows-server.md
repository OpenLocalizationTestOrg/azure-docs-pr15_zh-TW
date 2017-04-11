<properties
   pageTitle="建立及管理獨立 Azure 服務布料的轉印圖樣叢集 |Microsoft Azure"
   description="建立及管理 Azure 服務布料的轉印圖樣叢集 （實體或虛擬） 任何電腦上執行 Windows Server，無論您是內部部署或任何雲端。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>


# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>建立及管理叢集 Windows 伺服器上執行

若要建立任何虛擬機器或執行 Windows Server 的電腦上的服務布料的轉印圖樣叢集，您可以使用 Azure 服務布料的轉印圖樣。 這表示您可以部署並包含一組相互連接的 Windows Server 電腦的任何環境中執行服務布料的轉印圖樣的應用程式，其內部部署或雲端中的任何提供者。 服務架構提供建立服務布料的轉印圖樣叢集稱為獨立 Windows Server 套件的安裝套件。

本文會引導您藉由內部部署，服務結構獨立套件建立叢集，但它可以輕鬆地修正等其他雲端提供者的任何其他環境所需的步驟。

>[AZURE.NOTE] 此獨立 Windows Server 套件包含目前在預覽中並不支援的商業用途的功能。 在預覽中的功能清單，請參閱 「 預覽功能包含在此套件 」。 您也可以[下載一份合約](http://go.microsoft.com/fwlink/?LinkID=733084)現在。


<a id="getsupport"></a>
## <a name="get-support-for-the-service-fabric-standalone-package"></a>取得支援服務布料的轉印圖樣獨立套件

- 詢問社群服務布料的轉印圖樣獨立套件中[Azure 服務布料的轉印圖樣論壇，在](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)Windows Server。

- 開啟[服務布料的轉印圖樣的專業支援](http://support.microsoft.com/oas/default.aspx?prid=16146 )票證。  深入瞭解[Microsoft 的專業支援](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。

<a id="downloadpackage"></a>
## <a name="download-the-service-fabric-standalone-package"></a>下載服務布料的轉印圖樣獨立套件


[下載獨立套件服務布料的轉印圖樣的 Windows Server 2012 R2 及更新版本](http://go.microsoft.com/fwlink/?LinkId=730690)，稱為 Microsoft.Azure.ServiceFabric.WindowsServer。&lt;版本&gt;.zip。


下載套件中，您會看到下列檔案︰

|**檔案名稱**|**簡短描述**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|包含二進位檔案已部署至每一部電腦叢集之.cab 檔案。|
|ClusterConfig.Unsecure.DevCluster.json|叢集設定的範例檔案包含的設定不安全、 三節點單一電腦 （或虛擬機器） 開發叢集，包括叢集每個節點的資訊。 |
|ClusterConfig.Unsecure.MultiMachine.json|叢集設定的範例檔案包含的設定不安全、 多電腦 （或虛擬機器） 叢集，包括叢集每一部電腦的資訊。|
|ClusterConfig.Windows.DevCluster.json|叢集設定的範例檔案包含所有安全的三節點單一電腦 （或虛擬機器） 開發叢集設定，包括叢集中每個節點的資訊。 使用[Windows 身分識別](https://msdn.microsoft.com/library/ff649396.aspx)保護的安全叢集。|
|ClusterConfig.Windows.MultiMachine.json|包含使用 Windows 的安全性，包括每一部電腦的安全叢集資訊安全，多電腦 （或虛擬機器） 叢集的所有設定叢集設定的範例檔案。 使用[Windows 身分識別](https://msdn.microsoft.com/library/ff649396.aspx)保護的安全叢集。|
|ClusterConfig.x509.DevCluster.json|叢集設定的範例檔案包含所有安全的三節點單一電腦 （或虛擬機器） 開發叢集設定，包括叢集每個節點的資訊。 叢集受到保護的使用 x509 憑證。|
|ClusterConfig.x509.MultiMachine.json|包含安全、 多電腦 （或虛擬機器） 叢集，包括每個節點的資訊安全叢集中的所有設定叢集設定的範例檔案。 叢集受到保護的使用 x509 憑證。|
|EULA_ENU.txt|Microsoft Azure 服務布料的轉印圖樣獨立 Windows Server 套件的使用授權條款。 您可以[下載一份使用者授權合約](http://go.microsoft.com/fwlink/?LinkID=733084)。|
|Readme.txt|連結的版本資訊及基本的安裝指示。 則子集這份文件中的指示進行。|
|CreateServiceFabricCluster.ps1|建立使用設定中 ClusterConfig.json 叢集 PowerShell 指令碼。|
|RemoveServiceFabricCluster.ps1|移除使用 ClusterConfig.json 中的設定叢集 PowerShell 指令碼。|
|ThirdPartyNotice.rtf |爭議套件中的第三方軟體。|
|AddNode.ps1|新增到現有的節點的 PowerShell 指令碼部署叢集。|
|RemoveNode.ps1|從現有移除節點的 PowerShell 指令碼部署叢集。|
|CleanFabric.ps1|清除獨立版服務布料的轉印圖樣安裝目前的電腦關閉 PowerShell 指令碼。 應該使用自己的相關聯的 uninstallers 移除先前 MSI 安裝。|
|TestConfiguration.ps1|分析 Cluster.json 中所指定的基礎結構的 PowerShell 指令碼。|


## <a name="plan-and-prepare-your-cluster-deployment"></a>規劃及準備叢集部署
建立您的叢集之前，請執行下列步驟。

### <a name="step-1-plan-your-cluster-infrastructure"></a>步驟 1︰ 規劃叢集基礎結構
您即將建立您自己，電腦上的服務布料的轉印圖樣叢集，讓您決定要不受叢集何種失敗。 例如，您需要個別電源線或網際網路連線，提供給這些機器嗎？ 此外，請考慮下列機器的實體安全性。 電腦的位置，以及誰需要存取這些？ 決定之後，您可以以邏輯方式將機器對應到各種故障網域 （請參閱步驟 4）。 規劃生產叢集基礎結構便更深入的體驗比測試叢集的項目。

<a id="preparemachines"></a>
### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>步驟 2︰ 準備要符合的必要條件機器
您想要新增到叢集每一部電腦的先決條件︰

- 建議您為 16 GB 的 RAM 最小值。
- 建議您為 40 GB 的可用磁碟空間的最小值。
- 建議的 4 核心或更大的 CPU。
- 連線至安全的網路或網路上所有的電腦。
- （您需要有[KB2858668](https://support.microsoft.com/kb/2858668)安裝） 的 Windows Server 2012 R2 或 Windows Server 2012。
- [.NET framework 4.5.1 或更高](https://www.microsoft.com/download/details.aspx?id=40773)，完整的安裝。
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)。
- [RemoteRegistry 服務](https://technet.microsoft.com/library/cc754820)應該在所有電腦上執行。

部署及設定叢集叢集系統管理員必須具備[系統管理員權限](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx)，每一部電腦上。 您無法在網域控制站安裝服務布料的轉印圖樣。

### <a name="step-3-determine-the-initial-cluster-size"></a>步驟 3︰ 決定初始叢集大小
每個節點獨立版服務布料的轉印圖樣叢集中有服務布料的轉印圖樣執行階段部署及叢集的成員。 在一般生產部署中，有一個節點每個 OS 執行個體 （實體或虛擬）。 叢集大小取決於您的業務需求。 不過，您必須具有三個節點 （機器或虛擬機器） 的最小叢集大小。
為了開發，您可以指定電腦上有一個以上的節點。 在生產環境中，服務布料的轉印圖樣支援每個實體或虛擬機器只有一個節點。

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>步驟 4︰ 決定故障網域數目，並升級網域
*故障網域*(FD) 實體單位失敗，而直接關聯中的資料中心的實際基礎結構。 故障網域包含共用單一的失敗的硬體元件 （電腦、 選項、 網路等等）。 雖然沒有 1:1 對應故障網域和機架之間，彈性來說，每個機架可以視為故障網域。 考慮中叢集節點，我們強烈建議節點散佈至少三個故障網域。

當您指定 FDs ClusterConfig.json 中時，您可以選擇每個 FD 的名稱。 服務布料的轉印圖樣僅支援階層式 FDs，因此您可以反映您基礎結構的拓撲很。  例如，下列 FDs 是有效的︰

- 「 faultDomain 」: 「 fd: / Room1/Rack1/Machine1 」
- 「 faultDomain 」: 「 fd: / FD1 」
- 「 faultDomain 」: 「 fd: / Room1/Rack1/PDU1/M1 」


*將網域升級*（「 ud 」） 是邏輯節點的單位。 期間 （應用程式升級或叢集升級） 的服務布料的轉印圖樣協調升級，「 ud 」 中的所有節點將都來到向下節點其他 UDs 中的都會持續為可用服務要求時，請執行升級。 您在電腦執行的韌體升級不受影響 UDs，因此您必須執行這些電腦一次。

考量這些概念最簡單的方法是維護的考慮 FDs 為單位意外的錯誤和 UDs 做為預定進行單位。

當您指定 UDs ClusterConfig.json 中時，您可以選擇每個 「 ud 」 的名稱。 例如，下列名稱是有效的︰

- 「 upgradeDomain 」: 「 UD0 」
- 「 upgradeDomain 」: 「 UD1A 」
- 「 upgradeDomain 」: 「 DomainRed 」
- 「 upgradeDomain 」: 「 藍色 」

升級的網域和故障網域的詳細資訊，請參閱[說明服務布料的轉印圖樣叢集](service-fabric-cluster-resource-manager-cluster-description.md)。

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>步驟 5︰ 下載適用於 Windows Server 服務布料的轉印圖樣獨立套件
[下載 Windows Server 的服務布料的轉印圖樣獨立套件](http://go.microsoft.com/fwlink/?LinkId=730690)與解壓縮套件，不屬於叢集，部署電腦或其中一項會叢集部分的電腦。 您可以重新命名 [解壓縮] 資料夾`Microsoft.Azure.ServiceFabric.WindowsServer`。

<a id="createcluster"></a>
## <a name="create-your-cluster"></a>建立您的叢集

您已經完成的計劃與準備步驟之後，您就準備好要建立叢集。

### <a name="step-1-modify-cluster-configuration"></a>步驟 1︰ 修改叢集設定
叢集是由 ClusterConfig.json 所述。 如需此檔案中的章節上的詳細資訊，請參閱[獨立 Windows 叢集組態設定](service-fabric-cluster-manifest.md)。
開啟一個 ClusterConfig.json 檔案的下載並修改下列設定︰

<!--Loc Comment: Please, check that line 129 the clause has been modified to "that you use as placement constraints" instead of using "you are used as placement constraints"-->

|**設定的設定**|**描述**|
|-----------------------|--------------------------|
|**NodeTypes**|節點類型可讓您叢集節點分成各種不同的群組。 叢集必須至少有一個節點類型。 在群組中的所有節點都有下列共同的特性︰ <br> **名稱**-這是節點類型名稱。 <br>**端點連接埠**-這些是不同命名的結束點 （連接埠） 與此節點類型相關聯。 您可以使用任何您想，前提是他們不會與此資訊清單中的其他項目衝突，並不是在電腦/VM 上執行的其他應用程式所使用的連接埠號碼。 <br> **位置屬性**-其中說明您作為系統服務或服務的位置的限制式此節點類型的內容。 這些屬性會指定節點提供額外的中繼資料的使用者定義金鑰/值組。 節點屬性的範例是節點是否有硬碟或圖形卡，軸其硬碟、 核心和其他實體屬性中的數字。 <br> **容量**節點容量定義的名稱與特定的節點具有特定資源的數量可供使用。 例如有容量的度量單位，稱為 「 MemoryInMb 」，且其 2048 MB 的可用依預設，可能會定義節點。 這些容量在執行階段用於確保需要特定量的資源的服務會置於在所需的數量中使用這些資源的節點。<br>**IsPrimary** -如果您有多個節點類型定義確保只有一個會設定為主要的值*，則為 true*，即執行系統服務的位置。 所有其他節點類型應該設定值*，則為 false*|
|**節點**|這些是針對每個叢集 （節點類型、 節點名稱、 IP 位址、 故障網域和節點的升級的網域） 的一部分的節點的詳細資料。 電腦您想要在此處會列出使用它們的 IP 位址需要建立叢集。 <br> 如果您使用的所有節點的相同的 IP 位址，然後一個方塊叢集建立，您可以使用供測試之用。 請勿使用一個方塊叢集部署生產負載。|


### <a name="step-2-run-the-testconfiguration-script"></a>步驟 2︰ 執行 TestConfiguration 指令碼

TestConfiguration 指令碼會測試基礎結構，請確定所需的權限指派、 機器連接到彼此，及其他屬性的定義，以便能成功部署 cluster.json 中所定義。 基本上是迷你最佳作法分析。 我們會繼續這項工具新增更多驗證，使其更強大的時間。

具有系統管理員存取權會列為節點叢集設定檔中的所有電腦任何電腦可以執行這個指令碼。 電腦上執行這個指令碼可能沒有叢集的一部分。

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True


```

### <a name="step-3-run-the-create-cluster-script"></a>步驟 3︰ 執行建立叢集指令碼
您已修改叢集設定 JSON 文件中的，並且加入之後，所有的節點資訊執行叢集建立*CreateServiceFabricCluster.ps1* PowerShell 指令碼，從 [套件] 資料夾，然後傳遞路徑至 JSON 設定檔。 完成後，接受使用者授權合約。

具有系統管理員存取權會列為節點叢集設定檔中的所有電腦任何電腦可以執行這個指令碼。 電腦上執行這個指令碼可能沒有叢集的一部分。

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] 部署記錄，可在本機上 VM/之電腦執行 CreateServiceFabricCluster PowerShell。 他們的年齡稱為 DeploymentTraces 您執行的 PowerShell 命令的位置] 資料夾底下的子資料夾中。 若要查看服務布料的轉印圖樣是否已正確部署至電腦，您可以尋找已安裝的檔案在 C:\ProgramData 目錄中，而且能看到的 FabricHost.exe 和 Fabric.exe 程序，在 [工作管理員] 中執行。

### <a name="step-4-connect-to-the-cluster"></a>步驟 4︰ 連線到叢集

若要連線至安全叢集，請參閱[連線至安全叢集服務結構](service-fabric-connect-to-secure-cluster.md)。

若要連線至不安全叢集，執行下列 PowerShell 命令︰

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5-bring-up-service-fabric-explorer"></a>步驟 5︰ 顯示服務布料的轉印圖樣總管

現在您可以連線至服務布料的轉印圖樣檔案總管直接從機器 http://localhost:19080/Explorer/index.html 或遠端 http://< 其中叢集*IPAddressofaMachine*>: 19080/Explorer/index.html。



## <a name="add-and-remove-nodes"></a>新增和移除節點

您可以新增或移除您的業務需求的改變獨立版服務布料的轉印圖樣叢集] 節點。 如需詳細步驟，請參閱[新增或移除服務布料的轉印圖樣獨立叢集節點](service-fabric-cluster-windows-server-add-remove-nodes.md)。


## <a name="remove-a-cluster"></a>移除叢集

若要移除叢集，從套件資料夾執行*RemoveServiceFabricCluster.ps1* PowerShell 指令碼並傳遞路徑中 JSON 設定檔。 您也可以指定刪除的記錄檔的位置。

具有系統管理員存取權會列為節點叢集設定檔中的所有電腦任何電腦可以執行這個指令碼。 電腦上執行這個指令碼可能沒有叢集的一部分。

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>
## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>遙測資料收集，以及如何將其退出

做為預設值，產品會收集遙測改善產品服務布料的轉印圖樣流量。 執行的連線設定檢查項目至[https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)最佳作法分析。 如果您無法連線到，安裝失敗，除非您退出遙測。

  1. 將上傳下列資料至[https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)每天一次嘗試遙測管道的郵件。 它是最佳上傳並叢集功能造成任何影響。 遙測只會傳送從節點執行容錯移轉的主要管理員。 沒有其他節點送出遙測。

  2. 遙測包含下列各項︰

-            數字的服務
-            ServiceTypes 的數字
-            應用程式的數字
-            ApplicationUpgrades 的數字
-            FailoverUnits 的數字
-            InBuildFailoverUnits 的數字
-            UnhealthyFailoverUnits 的數字
-            數字的複本
-            InBuildReplicas 的數字
-            StandByReplicas 的數字
-            OfflineReplicas 的數字
-            CommonQueueLength
-            QueryQueueLength
-            FailoverUnitQueueLength
-            CommitQueueLength
-            節點數目
-            IsContextComplete: True/False
-            這是隨機產生的每個叢集 GUID ClusterId:
-            ServiceFabricVersion
-             虛擬機器或電腦上傳從遙測 IP 位址


若要停用遙測，新增下列您叢集設定的*屬性*︰ *enableTelemetry: false*。

<a id="previewfeatures"></a>
## <a name="preview-features-included-in-this-package"></a>在此套件中包含的預覽功能

無。

>[AZURE.NOTE] 以新[GA 版本的 Windows Server 的獨立叢集 (版本 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)，您可以叢集升級至未來的版本，以手動方式] 或 [自動。 此功能無法在預覽版本上使用，因為您必須使用 GA 版本建立叢集，並將您的資料與應用程式移轉的預覽。 請密切如需此功能的詳細資訊。


## <a name="next-steps"></a>後續步驟
- [獨立 Windows 叢集組態設定](service-fabric-cluster-manifest.md)
- [新增或移除獨立版服務布料的轉印圖樣叢集節點](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [執行 Windows Azure Vm 以建立獨立版服務布料的轉印圖樣叢集](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [安全獨立叢集使用 Windows 安全性的 Windows 上](service-fabric-windows-cluster-windows-security.md)
- [安全獨立叢集上 Windows 使用 X509 憑證](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
