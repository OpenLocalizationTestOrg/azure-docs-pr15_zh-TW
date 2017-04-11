<properties
   pageTitle="服務布料的轉印圖樣的應用程式部署 |Microsoft Azure"
   description="如何部署及移除應用程式中服務布料的轉印圖樣"
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

# <a name="deploy-and-remove-applications-using-powershell"></a>部署及移除應用程式使用 PowerShell

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

一次[應用程式類型已封裝][10]，準備好將 Azure 服務布料的轉印圖樣叢集部署。 部署涉及下列三個步驟︰

1. 上傳應用程式套件
2. 登錄應用程式類型
3. 建立應用程式執行個體

>[AZURE.NOTE] 如果您使用 Visual Studio 中部署及偵錯本機開發叢集上的應用程式時，會自動透過應用程式專案的指令碼] 資料夾中的 PowerShell 指令碼處理所有下列步驟。 本文提供背景上的指令碼，讓您可以執行相同作業以外 Visual Studio 所執行的動作。

## <a name="upload-the-application-package"></a>上傳應用程式套件

應用程式套件上傳會將其放在內部服務布料的轉印圖樣元件可存取的位置。 您可以使用 PowerShell 來執行上傳。 才能執行本文中的任何 PowerShell 命令，一律使用來啟動[連線 ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)連線至服務布料的轉印圖樣叢集。

假設您有一個名為*MyApplicationType*資料夾包含必要的應用程式資訊清單服務資訊清單中，與程式碼/設定資料套件。 [複製 ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125905.aspx) ] 命令上傳到叢集市集圖像的套件。 **取得 ImageStoreConnectionStringFromClusterManifest** cmdlet，這是服務布料的轉印圖樣 SDK PowerShell 模組的一部分，可用來取得圖像市集連線字串。  若要匯入 SDK 模組，請執行︰

```
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

您可以複製應用程式套件*C:\users\ryanwi\Documents\Visual Studio 2015\Projects\MyApplication\myapplication\pkg\debug*到*c:\temp\MyApplicationType* （重新命名 「 MyApplicationType 」 的 「 偵錯 」 目錄）。 下列範例上傳的套件︰

~~~
PS C:\temp> dir

    Directory: c:\temp


Mode                LastWriteTime         Length Name                                                                                   
----                -------------         ------ ----                                                                                   
d-----        8/12/2016  10:23 AM                MyApplicationType                                                                          

PS C:\temp> tree /f .\Stateless1Pkg

C:\TEMP\MyApplicationType
│   ApplicationManifest.xml
|
└───Stateless1Pkg
  	|   ServiceManifest.xml
    │
    └───Code
    │   │  Microsoft.ServiceFabric.Data.dll
    │   │  Microsoft.ServiceFabric.Data.Interfaces.dll
    │   │  Microsoft.ServiceFabric.Internal.dll
    │   │  Microsoft.ServiceFabric.Internal.Strings.dll
    │   │  Microsoft.ServiceFabric.Services.dll
    │   │  ServiceFabricServiceModel.dll
    │   │  MyService.exe
    │   │  MyService.exe.config
    │   │  MyService.pdb
    │   │  System.Fabric.dll
    │   │  System.Fabric.Strings.dll
    │   │
    │   └───en-us
  	|         Microsoft.ServiceFabric.Internal.Strings.resources.dll
  	|         System.Fabric.Strings.resources.dll
  	|
    ├───Config
    │     Settings.xml
    │
    └───Data
          init.dat

PS C:\temp> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath MyApplicationType -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest))
Copy application package succeeded

PS D:\temp>
~~~

## <a name="register-the-application-package"></a>登錄應用程式套件

註冊應用程式套件，可讓您在應用程式類型及宣告可用應用程式資訊清單中使用的版本。 系統會讀取套件上傳前一個步驟、 驗證套件 （相當於本機執行[測試 ServiceFabricApplicationPackage](https://msdn.microsoft.com/library/mt125950.aspx) ）、 封裝內容]、 [處理程序，複製處理的套件至內部系統位置。

~~~
PS D:\temp> Register-ServiceFabricApplicationType MyApplicationType
Register application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp>
~~~

系統已成功複製應用程式套件之後，才會傳回[Register ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125958.aspx) ] 命令。 這需要耗費取決於應用程式套件的內容。 如有需要**-TimeoutSec**參數可提供較長的逾時。 （預設逾時是 60 秒）。

[取得 ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125871.aspx)命令可列出所有成功註冊的應用程式類型版本。

## <a name="create-the-application"></a>建立應用程式

您可以使用任何已透過[新增 ServiceFabricApplication](https://msdn.microsoft.com/library/mt125913.aspx)命令順利註冊的應用程式類型版本初始化應用程式。 每個應用程式名稱必須開始*布料的轉印圖樣︰*配置和是唯一的每個應用程式執行個體。 這次被建立目標應用程式類型的應用程式清單中所定義任何預設的服務。

~~~
PS D:\temp> New-ServiceFabricApplication fabric:/MyApp MyApplicationType AppManifestVersion1

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
ApplicationStatus      : Ready
HealthState            : OK
ApplicationParameters  : {}

PS D:\temp> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/MyService
ServiceKind            : Stateless
ServiceTypeName        : MyServiceType
IsServiceGroup         : False
ServiceManifestVersion : SvcManifestVersion1
ServiceStatus          : Active
HealthState            : Ok

PS D:\temp>
~~~

[取得 ServiceFabricApplication](https://msdn.microsoft.com/library/mt163515.aspx)命令可列出所有應用程式執行個體順利建立的以及他們整體的狀態。

[取得 ServiceFabricService](https://msdn.microsoft.com/library/mt125889.aspx)命令可列出已成功建立特定的應用程式的執行個體中的所有服務執行個體。 預設的服務 （如果有的話） 列示如下所示。

註冊的應用程式任何的類型指定版本，可以建立多個應用程式執行個體。 每個應用程式執行個體隔離執行的以自己的工作目錄和程序。

## <a name="remove-an-application"></a>移除應用程式

時已不再需要的應用程式執行個體，您可以使用 [[移除 ServiceFabricApplication](https://msdn.microsoft.com/library/mt125914.aspx) ] 命令將會永久移除框線。 這個命令會自動移除所有的服務，屬於應用程式，將會永久移除所有的服務狀態。 無法回復這項作業，並無法復原應用程式狀態。

~~~
PS D:\temp> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS D:\temp> Get-ServiceFabricApplication
PS D:\temp>
~~~

當已不再需要特定版本的應用程式類型時，您應該取消其使用[取消註冊 ServiceFabricApplicationType](https://msdn.microsoft.com/library/mt125885.aspx) ] 命令。 取消註冊未使用過的類型釋放該類型圖像存放區上的應用程式套件的內容所使用的儲存空間。 只要沒有應用程式會產生，並且不擱置的應用程式升級參照可以取消登錄應用程式類型。

~~~
PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : AppManifestVersion1
DefaultParameters      : {}

PS D:\temp> Unregister-ServiceFabricApplicationType MyApplicationType AppManifestVersion1
Unregister application type succeeded

PS D:\temp> Get-ServiceFabricApplicationType

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v1
DefaultParameters      : {}

ApplicationTypeName    : DemoAppType
ApplicationTypeVersion : v2
DefaultParameters      : {}

PS D:\temp>
~~~

## <a name="troubleshooting"></a>疑難排解

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>複製 ServiceFabricApplicationPackage 要求 ImageStoreConnectionString

服務布料的轉印圖樣 SDK 環境應該已正確設定的預設值。 但是，所有命令 ImageStoreConnectionString 如有需要應該相符的值，使用的服務布料的轉印圖樣叢集。 您可以找到這透過[取得 ServiceFabricClusterManifest](https://msdn.microsoft.com/library/mt126024.aspx)命令擷取叢集資訊清單中︰

~~~
PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType

cmdlet Copy-ServiceFabricApplicationPackage at command pipeline position 1
Supply values for the following parameters:
ImageStoreConnectionString:

PS D:\temp> Get-ServiceFabricClusterManifest
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]

PS D:\temp> Copy-ServiceFabricApplicationPackage .\MyApplicationType -ImageStoreConnectionString file:D:\ServiceFabric\Data\ImageStore
Copy application package succeeded

PS D:\temp>
~~~

## <a name="next-steps"></a>後續步驟

[服務布料的轉印圖樣應用程式升級](service-fabric-application-upgrade.md)

[服務布料的轉印圖樣狀況簡介](service-fabric-health-introduction.md)

[診斷和疑難排解服務布料的轉印圖樣服務](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[模型中服務布料的轉印圖樣的應用程式](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-application-model.md
[11]: service-fabric-application-upgrade.md
