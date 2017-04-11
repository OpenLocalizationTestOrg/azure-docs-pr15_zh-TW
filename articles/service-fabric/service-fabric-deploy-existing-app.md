<properties
   pageTitle="將現有的可執行檔部署至 Azure 服務布料的轉印圖樣 |Microsoft Azure"
   description="如何讓它可以部署至服務布料的轉印圖樣叢集封裝現有的應用程式可執行檔，以來賓身分的逐步解說"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>

# <a name="deploy-a-guest-executable-to-service-fabric"></a>將可執行以來賓部署至服務布料的轉印圖樣

您可以執行任何類型的應用程式，例如 node.js、 Java 或原生應用程式中 Azure 服務布料的轉印圖樣。 服務布料的轉印圖樣指的是這些應用程式類型為來賓可執行檔。
來賓可執行檔是由服務布料的轉印圖樣視為無狀態服務。 如此一來，他們會置於叢集，根據可用性和其他標準的節點。 本文將說明如何封裝並可執行以來賓部署至服務布料的轉印圖樣叢集，使用 Visual Studio 或命令列公用程式。

本文涵蓋封裝來賓可執行，並將其部署到服務布料的轉印圖樣的步驟。  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>執行以來賓可在服務結構的優點

有了與執行服務布料的轉印圖樣叢集可執行以來賓的幾項優點︰

- 高的可用性。 執行服務結構中的應用程式會提供高度。 服務布料的轉印圖樣可確保執行的一個應用程式的執行個體。
- 監控健康狀況。 服務布料的轉印圖樣健康監視偵測如果應用程式正在執行，並提供的診斷資訊，如果失敗。   
- 應用程式生命週期管理。 除了提供升級不中斷的情況，服務布料的轉印圖樣會提供到前一版的自動回復，如果在升級期間報告錯誤狀況事件。    
- 密度。 您可以執行多個應用程式中叢集，就不需要在自己的硬體上執行的每個應用程式。


## <a name="overview-of-application-and-service-manifest-files"></a>應用程式與服務的資訊清單檔案的概觀

部署可執行以來賓的一部分，幫助以了解服務布料的轉印圖樣封裝並部署模型，以說明[應用程式模型](service-fabric-application-model.md)。 服務布料的轉印圖樣包裝模型依賴兩個 XML 檔案︰ 應用程式和服務的資訊清單。 使用服務布料的轉印圖樣 SDK 安裝的 ApplicationManifest.xml 和 ServiceManifest.xml 檔案的結構描述定義至*C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*。

* **應用程式資訊清單**應用程式顯示用來描述應用程式。 它會列出撰寫它和其他用來定義如何一或多個服務應該部署，例如執行個體數目的參數的服務。

  在服務布料的轉印圖樣應用程式會是單位的部署及升級。 可作為單一單位可能失敗，並可能復原哪裡管理升級應用程式。 服務布料的轉印圖樣保證升級程序，是成功，或者，如果升級失敗時，不會讓應用程式中未知/不穩定的狀態。

* **服務資訊清單**服務資訊清單描述服務的元件。 其包含資料，例如的名稱與服務，其程式碼、 設定及資料類型。 服務資訊清單也會包含可用來設定服務後部署一些其他參數。


## <a name="application-package-file-structure"></a>應用程式套件檔案結構
若要部署服務布料的轉印圖樣應用程式，應用程式必須遵循的預先定義的目錄結構。 下列範例該結構。

```
|-- ApplicationPackageRoot
  	|-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```

ApplicationPackageRoot 包含 ApplicationManifest.xml 檔案的應用程式。 每個服務應用程式中包含子目錄用來包含所有的成品服務所需-ServiceManifest.xml 和一般而言，下列三個目錄︰

- *程式碼*。 此目錄中包含服務代碼。
- *設定*。 此目錄包含 Settings.xml 檔案 （及其他檔案，如有需要） 服務可以存取在執行階段以擷取特定設定的設定。
- *資料*。 這是額外的目錄服務可能會需要的其他本機資料的儲存。 附註︰ 資料應該用來儲存暫時的資料。 服務布料的轉印圖樣不會無法複製/複寫到資料目錄的變更如果服務必須重新定位，例如，在容錯移轉。

注意︰ 您沒有建立`config`和`data`如果您不需要進行的目錄。

## <a name="packaging-an-existing-executable"></a>包裝的現有的可執行檔

當包裝來賓可執行檔，您可以選擇使用 Visual Studio 專案範本或[以手動方式建立應用程式套件](#manually)。 使用 Visual Studio，應用程式套件結構及資訊清單的檔案，會建立您的新專案精靈。

>[AZURE.NOTE] 封裝現有的 Windows 服務可執行最簡單的方法是使用 Visual Studio。

## <a name="using-visual-studio-to-package-an-existing-executable"></a>使用 Visual Studio 封裝現有的可執行檔

Visual Studio 提供服務布料的轉印圖樣服務範本，以協助您部署可執行以來賓服務布料的轉印圖樣叢集。

請執行下列步驟以完成發佈︰

1. 選擇 [檔案]-> [新的專案，並建立服務布料的轉印圖樣應用程式。
2. 選擇來賓可執行檔作為服務範本。
3. 按一下 [瀏覽] 以選取與您可執行檔資料夾，然後填寫其餘的參數建立服務]。
    - 將資料夾的所有內容複製到 Visual Studio 專案，適合如果可執行檔不會變更，可以設定*程式碼套件行為*。 如果您要變更，並可選擇新建置動態可執行檔，您可以選擇要改為連結的資料夾。 請注意，在 Visual Studio 建立應用程式專案時，您可以使用連結的資料夾。 從來源位置，在專案中，讓您更新可執行訪客來源目的，此連結無法成為建置應用程式套件的一部分的更新。
    - *程式*-選擇可執行檔應該執行啟動服務。
    - *引數*的指定應該可執行檔傳遞的引數。 可能之引數的參數的清單。
    - *WorkingFolder* -指定的工作目錄前往啟動程序。 您可以指定三個值︰
        - `CodeBase`指定的工作目錄要設為應用程式套件的程式碼目錄 (`Code`目錄中顯示前面的檔案結構)。
        - `CodePackage`指定的工作目錄要設為應用程式套件的根目錄 (`GuestService1Pkg`顯示前面檔案結構中)。
        - `Work`指定檔案位於子目錄稱為工作
4. 為您的服務名稱，然後按一下 [確定]。
5. 如果您的服務需要通訊的端點，您現在可以 ServiceManifest.xml 檔案來新增通訊協定、 連接埠與類型。 e.g. `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. 您現在可以使用封裝並偵錯方案 Visual Studio 中的，針對您的本機叢集發佈動作。 準備好時，即可發佈到遠端叢集應用程式或存回來源控制項的解決方案。
7. 移至本文，瞭解如何檢視您在服務布料的轉印圖樣檔案總管中執行的來賓可執行服務的結尾。

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-an-existing-executable"></a>手動封裝和部署現有的可執行檔
手動封裝來賓可執行的程序會根據下列步驟︰

1. 建立套件目錄結構。
2. 新增應用程式的程式碼及設定的檔案。
3. 編輯服務資訊清單檔案。
4. 編輯應用程式資訊清單檔案。

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>建立套件目錄結構
您可以開始建立目錄結構稍早所述。

### <a name="add-the-applications-code-and-configuration-files"></a>新增應用程式的程式碼和設定檔案
您建立的目錄結構之後，您可以新增的應用程式碼和程式碼] 和 [config 目錄下的設定檔。 您也可以建立其他目錄或程式碼或設定目錄子目錄。

服務布料的轉印圖樣會內容的應用程式的根目錄，xcopy，因此沒有預先定義的結構，若要使用其他比建立兩個上方目錄、 程式碼和設定。 （您可以挑選不同的名稱如果您想要。 更多詳細資料位於下一節。）

>[AZURE.NOTE] 請確定您已包含所有檔案/相依性的應用程式需要。 服務布料的轉印圖樣叢集應用程式的服務要部署中，複製所有節點上的應用程式套件的內容。 套件包含所有的應用程式必須執行的程式碼。 我們不建議假設已安裝的相依性。

### <a name="edit-the-service-manifest-file"></a>編輯服務資訊清單檔案
下一步是編輯服務資訊清單檔案包含下列資訊︰

- 服務類型的名稱。 這是用來識別服務的服務布料的轉印圖樣的識別碼。
- 要啟動應用程式 (ExeHost) 使用的命令。
- 必須為執行任何指令碼向上 / 應用程式設定 (SetupEntrypoint)。

以下是範例`ServiceManifest.xml`檔案︰

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

我們跳上的檔案，您需要更新的不同部分︰

#### <a name="updating-the-servicetypes"></a>更新 ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- 您可以選擇您想要的任何名稱`ServiceTypeName`。 會使用中的值`ApplicationManifest.xml`來識別服務的檔案。
- 您必須指定`UseImplicitHost="true"`。 這個屬性會告知服務布料的轉印圖樣做的所有服務布料的轉印圖樣需求為啟動，以程序，並監控其健康情況的服務為基礎獨立的應用程式。

#### <a name="updating-the-codepackage"></a>更新 CodePackage
CodePackage 項目會指定服務的程式碼的位置 （和版本）。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name`項目用來指定目錄的名稱中包含服務的程式碼的應用程式套件。 `CodePackage`也有`version`屬性。 這可以用於指定的程式碼，版本，也可能會用於使用服務布料的轉印圖樣的應用程式生命週期管理基礎結構升級服務的程式碼。
#### <a name="optional-updating-the-setupentrypoint"></a>可省略︰ 更新 SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint 項目用來指定之前啟動服務的程式碼應該先執行任何可執行檔或批次檔案。 這樣就不需要不初始化/需要設定是否要包含，則是選擇性的步驟。 每次重新啟動服務時，會執行 SetupEntryPoint。

沒有只有一個 SetupEntrypoint，因此設定/config 指令碼需要分組單一批次檔案中，如果應用程式的安裝程式/config 需要多個指令碼。 SetupEntrypoint 可以執行任何類型的檔案--可執行檔、 批次檔案，以及 PowerShell cmdlet。 如需詳細資訊，請參閱[設定 SetupEntryPoint](service-fabric-application-runas-security.md)。

在上述範例中，SetupEntrypoint 執行批次檔案稱為`LaunchConfig.cmd`的位於`scripts`（假設 WorkingFolder 項目設定為 [程式碼基底） 的程式碼目錄的子目錄。

#### <a name="updating-the-entrypoint"></a>更新進入點

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

`Entrypoint`服務資訊清單檔案中的項目用來指定如何啟動服務。 `ExeHost`項目會指定可執行檔 （和引數），應用於啟動服務。

- `Program`指定應該執行啟動服務的可執行檔的名稱。
- `Arguments`指定應該可執行檔傳遞的引數。 可能之引數的參數的清單。
- `WorkingFolder`指定要啟動的程序的工作目錄。 您可以指定三個值︰
    - `CodeBase`指定的工作目錄要設為應用程式套件的程式碼目錄 (`Code`目錄中的上一個檔案結構)。
    - `CodePackage`指定的工作目錄要設為應用程式套件的根目錄 (`GuestService1Pkg`上述檔案結構中)。
  - `Work`指定檔案位於子目錄稱為工作

WorkingFolder 很有用設定正確的工作目錄，以便相對路徑就能使用的應用程式 」 或 「 初始化指令碼。

#### <a name="updating-the-endpoints-and-registering-with-naming-service-for-communication"></a>更新結束點與通訊註冊命名服務

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
在上述範例`Endpoint`項目會指定應用程式可以聆聽結束點。 在此範例中，在連接埠 3000 http 接聽 Node.js 應用程式。

此外，您可以要求服務，將此端點發佈至命名服務，讓其他服務才能探索此服務端點位址布料的轉印圖樣。 這可讓您能夠與之間來賓可執行檔的服務。
已發佈的端點位址是表單`UriScheme://IPAddressOrFQDN:Port/PathSuffix`。 `UriScheme`與`PathSuffix`是選擇性的屬性。 `IPAddressOrFQDN`為 IPAddress 或完整網域名稱的這個可執行檔取得置於，而且會針對您計算的節點。

在下列範例後部署服務，在服務布料的轉印圖樣總管您會看到類似端點`http://10.1.4.92:3000/myapp/`發佈服務執行個體，或如果這是本機電腦，您看到`http://localhost:3000/myapp/`。 

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
您可以使用這些地址[反向 proxy](service-fabric-reverseproxy.md)服務之間的通訊。

### <a name="edit-the-application-manifest-file"></a>編輯應用程式資訊清單檔案

一旦您設定了`Servicemanifest.xml`檔案，您需要進行一些變更`ApplicationManifest.xml`檔案，以確定用正確的服務類型和名稱。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

在 [`ServiceManifestImport`項目，您可以指定一個或多個您想要的應用程式中包含的服務。 使用參考的服務`ServiceManifestName`，以指定目錄的名稱位置`ServiceManifest.xml`檔案。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>設定記錄
來賓可執行檔，最好能夠看到主控台記錄檔找出如果應用程式及設定的指令碼會顯示任何錯誤。
您可以設定主控台重新導向`ServiceManifest.xml`檔案使用`ConsoleRedirection`項目。

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* `ConsoleRedirection`可用於主控台輸出 （stdout 和 stderr） 至工作目錄重新導向，讓他們可以用來確認安裝程式或服務布料的轉印圖樣叢集應用程式的執行期間沒有錯誤。

    * `FileRetentionCount`決定如何多個檔案會儲存在工作目錄。 5 的值，表示的工作目錄中儲存先前的五個執行的記錄檔。
    * `FileMaxSizeInKb`指定最大值的記錄檔的大小。

在其中一項服務的使用目錄中儲存記錄檔。 若要判斷檔案的位置，您需要使用服務布料的轉印圖樣檔案總管來判斷哪一個節點上執行的服務，並確認使用的是哪個工作目錄。 本文稍後的涵蓋這個程序。

## <a name="deployment"></a>部署
最後一個步驟是部署您的應用程式。 下列 PowerShell 指令碼會顯示如何部署到本機開發叢集應用程式，並開始新的服務布料的轉印圖樣服務。

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
服務布料的轉印圖樣服務可以部署各種 「 設定 」。 例如，可以部署為單一或多個執行個體，或可以部署的方式有一個執行個體上每個服務布料的轉印圖樣叢集節點的服務。

`InstanceCount`的參數`New-ServiceFabricService`指令程式可用來指定應在服務布料的轉印圖樣叢集啟動服務的執行個體數目。 您可以設定`InstanceCount`值，視您要部署的應用程式的類型而定。 兩個常見的情況如下︰

* `InstanceCount = "1"`. 在此案例中，只有一個執行個體服務已部署叢集。 服務布料的轉印圖樣的排程器會決定服務前往部署在哪一個節點。

* `InstanceCount ="-1"`. 在此情況下的每個服務布料的轉印圖樣叢集節點部署服務的執行個體之一。 結果叢集有一個 （和只有一個） 執行個體的每個節點的服務。

這是有用的組態前端應用程式 （例如，其餘結束點），因為需要 「 連線 「 任何叢集使用端點中節點的用戶端應用程式。 也可以用這項設定，例如，服務布料的轉印圖樣叢集的所有節點都連線至時負載平衡器，用戶端流量由叢集內所有節點執行的服務。

## <a name="check-your-running-application"></a>檢查您執行的應用程式

在服務布料的轉印圖樣總管] 中，找出正在執行服務的節點。 在此範例中，執行 Node1 上︰

![節點正在執行服務](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

如果您瀏覽至節點，並瀏覽至應用程式，您會看到的基本節點資訊，包括其磁碟上的位置。

![磁碟上的位置](./media/service-fabric-deploy-existing-app/locationondisk2.png)

如果您使用瀏覽至目錄伺服器總管，您可以找到工作目錄服務的記錄檔資料夾依下列顯示的圖片。

![記錄的位置](./media/service-fabric-deploy-existing-app/loglocation.png)


## <a name="next-steps"></a>後續步驟
本文中，您已經學會如何封裝來賓可執行檔，然後將其部署到服務布料的轉印圖樣。 下一步，您可以查看此主題的其他內容。

- [封裝和部署上 GitHub 可執行以來賓的範例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)，包括搶鮮版包裝工具的連結
- [部署多個來賓可執行檔](service-fabric-deploy-multiple-apps.md)
- [建立第一個服務布料的轉印圖樣應用程式使用 Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
