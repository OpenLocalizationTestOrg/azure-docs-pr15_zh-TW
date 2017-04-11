<properties
   pageTitle="服務布料的轉印圖樣應用程式模型 |Microsoft Azure"
   description="如何建立模型，並說明應用程式與服務布料的轉印圖樣的服務。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# <a name="model-an-application-in-service-fabric"></a>模型中服務布料的轉印圖樣的應用程式

本文提供 Azure 服務布料的轉印圖樣應用程式模型的概觀。 也將說明如何定義的應用程式和服務，透過資訊清單的檔案，並取得應用程式，而且並準備好進行部署。

## <a name="understand-the-application-model"></a>瞭解此應用程式模型

應用程式是執行特定函數的構成服務的集合。 服務會執行完整及獨立的功能，（它可以啟動和執行其他服務亦） 和是由程式碼、 設定及資料。 每個服務的程式碼由可執行的二進位檔案、 設定包含可以在執行階段，載入的服務設定和資料組成任意靜態資料使用的服務。 在此階層式應用程式模型中的每個元件都可以建立版本與升級獨立。

![服務布料的轉印圖樣應用程式模型][appmodel-diagram]


應用程式類型的應用程式分類，而組成的服務類型集結。 服務類型是服務的分類。 分類可以有不同的設定] 和 [設定，但是核心功能保持不變。 服務的執行個體是不同的服務設定變化的同一個服務類型。  

課程 （或 [類型]） 應用程式和服務描述至 XML 檔案 （應用程式資訊清單和服務的資訊清單） 的應用程式可以產生叢集的圖像存放區中的範本。 結構描述定義為 ServiceManifest.xml 和 ApplicationManifest.xml 檔案已安裝的服務布料的轉印圖樣 SDK 和*C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*工具。

不同的應用程式的執行個體的程式碼會為另一個程序，即使是由同一個服務布料的轉印圖樣節點主控執行。 此外，每個應用程式執行個體的生命週期可管理 （亦即 獨立升級）。 下圖顯示的應用程式類型如何組成服務類型]，依序組成的程式碼、 設定及套件。 若要簡化圖表，只有的程式碼/config/資料封裝的`ServiceType4`會顯示，但每個服務類型包含部分或所有人員的封裝類型。

![服務布料的轉印圖樣應用程式類型與服務類型][cluster-imagestore-apptypes]

兩個不同的資訊清單檔案用來說明應用程式與服務︰ 服務資訊清單和應用程式資訊清單。 這些會包含在後續各節中的詳細資料。

可以有一或多個執行個體的 [服務類型叢集作用中。 例如，狀態服務執行個體或複本，獲得高可靠性複本位於不同的節點叢集之間複製狀態。 此複寫基本上提供多餘的設為可用，即使有一個節點叢集失敗的服務。 [分割的服務](service-fabric-concepts-partitioning.md)進一步會將各叢集節點其狀態 （和存取模式的狀態）。

下圖顯示應用程式與服務執行個體、 分割和複本之間的關聯。

![分割和服務中的複本][cluster-application-instances]


>[AZURE.TIP] 您可以檢視應用程式的版面配置中使用提供的服務布料的轉印圖樣 Explorer 工具在 http://&lt;yourclusteraddress&gt;: 19080/檔案總管。 如需詳細資訊，請參閱[視覺化叢集服務布料的轉印圖樣檔案總管](service-fabric-visualizing-your-cluster.md)。

## <a name="describe-a-service"></a>說明服務

服務宣告方式定義的服務類型和版本。 它會指定服務中繼資料，例如服務類型、 狀況屬性、 負載平衡指標、 服務的二進位檔案及設定檔。  將另一種方法，說明撰寫服務套件支援一或多個服務類型的程式碼、 設定及資料套件。 以下是簡單範例服務資訊清單︰

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

**版本**屬性結構的字串，而且無法剖析系統。 以下是您用於版本每個元件都升級。

**ServiceTypes**宣告支援這項資訊清單中的**CodePackages**哪些服務類型。 當服務會針對這些服務類型，此資訊清單中宣告的所有程式碼套件會啟用執行其進入點。 產生的程序都必須在執行階段註冊支援的服務類型。 請注意，服務類型宣告資訊清單層級，不是程式碼套件層級。 因此，多個程式碼套件時，其所有啟動時，系統會宣告的服務任何的類型一個。

**SetupEntryPoint**是執行服務布料的轉印圖樣 （通常是*LocalSystem*帳戶） 作為任何其他項目點之前相同的認證與權限的進入點。 可執行檔指定**進入點**通常是較長時間執行服務主機。 目前狀態的個別設定進入點可避免使用高的權限執行服務主機延伸一段時間。 **SetupEntryPoint**結束成功後，會執行可執行檔指定**進入點**。 產生的程序監視，而且如果時結束，或當機，請重新啟動 （開頭一次為**SetupEntryPoint**）。

**DataPackage**宣告資料夾，名為**名稱**屬性，包含要使用的程序在執行階段的任意靜態資料。

**ConfigPackage**宣告項名為 「**名稱**屬性，包含*Settings.xml*檔案的資料夾。 這個檔案包含使用者定義、 關鍵值組設定程序可讀取回在執行階段的區段。 升級期間，如果僅**ConfigPackage** **版本**已變更，然後執行的程序不會重新啟動。 不過，回撥通知，讓他們可以重新載入動態變更的設定，設定的程序。 以下是範例*Settings.xml*檔案︰

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] 服務資訊清單可以包含多個程式碼、 設定及資料套件。 每個可設定版本獨立。

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application"></a>描述 [應用程式


應用程式顯示宣告方式說明的應用程式類型和版本。 它會指定服務撰寫中繼資料，例如穩定分割配置、 執行個體計數/複寫因子變異數、 安全性/隔離原則、 位置的限制式、 設定覆寫和構成服務類型的名稱。 放置應用程式的負載平衡的網域也說明。

因此，應用程式資訊說明應用程式層級的項目，並且參照一或多個服務資訊清單，若要撰寫應用程式類型。 以下是簡單範例應用程式資訊清單︰

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

服務資訊清單，例如**版本**屬性結構的字串，而且不會剖析系統。 這些也是用來版本每個元件都升級。

**ServiceManifestImport**包含服務資訊撰寫此應用程式類型之清單的參照。 匯入的服務的資訊清單決定哪些服務類型會在此應用程式類型。

**DefaultServices**宣告每當針對此應用程式類型產生應用程式時自動建立的服務執行個體。 預設服務只方便之後已建立, 類似的每個正常服務。 他們會升級，以及任何其他服務中的應用程式執行個體，也可以移除。

> [AZURE.NOTE] 應用程式資訊可以包含多個服務的資訊清單匯入與預設的服務。 每個服務的資訊清單匯入可獨立版本。

若要瞭解如何維持不同的應用程式和服務個別環境中的參數，請參閱[管理多個環境的應用程式參數](service-fabric-manage-multiple-environment-app-configuration.md)。

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## <a name="package-an-application"></a>應用程式套件

### <a name="package-layout"></a>封裝版面配置

應用程式顯示、 服務 manifest(s) 及其他必要的套件檔案必須組織中部署至服務布料的轉印圖樣叢集特定的版面配置。 本文中的範例資訊清單需要組織目錄結構如下︰

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

資料夾的命名以符合的每個對應的項目**名稱**屬性。 例如，如果服務資訊清單包含兩個的程式碼套件**MyCodeA**和**MyCodeB**的名稱，然後兩個資料夾相同的名稱會包含每個程式碼封裝必要的二進位檔案。

### <a name="use-setupentrypoint"></a>使用 SetupEntryPoint

使用**SetupEntryPoint**的一般案例是您需要執行可執行檔服務開始之前，或是您需要執行提高權限的權限的操作。 例如︰

- 設定與初始化服務可執行檔需要的環境變數。 這是不限於只中可執行檔撰寫透過服務布料的轉印圖樣程式設計模型。 例如，npm.exe 需要環境變數部署 node.js 應用程式的設定。

- 設定存取控制由安裝安全性憑證。

### <a name="build-a-package-by-using-visual-studio"></a>使用 Visual Studio 建立套件

如果您使用 Visual Studio 2015 建立應用程式時，您可以使用 [套件] 命令會自動建立套件符合上述的版面配置。

若要建立套件，應用程式專案總管] 中的以滑鼠右鍵按一下，然後選擇 [套件] 命令，如下所示︰

![包裝具有 Visual Studio 的應用程式][vs-package-command]

包裝完成後，您會發現**輸出**視窗中的套件的位置。 請注意包裝步驟會自動當您部署或偵錯 Visual Studio 中的應用程式。

### <a name="test-the-package"></a>測試套件

您可以使用 [**測試 ServiceFabricApplicationPackage** ] 命令來驗證本機透過 PowerShell 套件結構。 這個命令會檢查剖析問題的資訊清單，並確認所有參照。 請注意，此命令僅會驗證結構的正確性目錄和套件中的檔案。 不會驗證的任何超出檢查所有必要的檔案會展示的程式碼或資料封裝內容。

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

此錯誤會顯示*MySetup.bat*檔案中的服務資訊清單**SetupEntryPoint**參照遺漏的程式碼。 加入遺失的檔案之後，應用程式驗證傳遞︰

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

應用程式正確封裝並通過驗證之後，就準備好進行部署。

## <a name="next-steps"></a>後續步驟

[部署及移除應用程式][10]

[管理多個環境的應用程式參數][11]

[RunAs︰ 執行服務布料的轉印圖樣應用程式使用不同的安全性權限][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
