<properties
    pageTitle="什麼是雲端服務模型和套件 |Microsoft Azure"
    description="將說明的雲端服務模型 （.csdef、.cscfg） 及封裝 (.cspkg) Azure 中"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>什麼是雲端服務模型，以及如何封裝？
在雲端服務會建立三個元件、 服務定義_(.csdef)_、 服務設定_(.cscfg)_，與服務套件_(.cspkg)_。 [ **ServiceDefinition.csdef** ] 和 [ **ServiceConfig.cscfg**檔案會以 XML 為基礎，並說明的結構，雲端服務，以及如何設定;統稱模型。 **ServicePackage.cspkg**是從**ServiceDefinition.csdef**和其他動作，則會產生，包含所有必要的二進位型相依性的 zip 檔案。 Azure 從**ServicePackage.cspkg**和**ServiceConfig.cscfg**建立雲端服務。

一旦在 Azure 執行雲端服務，您可以透過 [ **ServiceConfig.cscfg**檔案]，重新設定，但您無法變更定義。

## <a name="what-would-you-like-to-know-more-about"></a>您想要進一步瞭解？

* 我想深入瞭解的[ServiceDefinition.csdef](#csdef)和[ServiceConfig.cscfg](#cscfg)檔案。
* 我已經瞭解的，讓我在我可以設定的[一些範例](#next-steps)。
* 我想要建立[ServicePackage.cspkg](#cspkg)。
* 我使用 Visual Studio，且想要...
    * [建立新的雲端服務][vs_create]
    * [重新設定現有的雲端服務][vs_reconfigure]
    * [部署雲端服務專案][vs_deploy]
    * [遠端桌面到雲端服務的執行個體][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef**檔案指定會使用 Azure 設定雲端服務的設定。 [Azure 服務定義結構描述 (.csdef 檔案)](https://msdn.microsoft.com/library/azure/ee758711.aspx)提供的服務定義檔案格式。 下列範例顯示可以定義的網頁和背景工作角色的設定︰

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

您可以參考 [服務定義結構描述]]，進一步了解使用以下的 XML 結構描述，不過，以下是快速說明的某些項目︰

**網站**  
包含網站或 web 應用程式中 IIS7 主控的定義。

**InputEndpoints**  
包含定義連絡雲端服務所使用的結束點。

**InternalEndpoints**  
包含定義會使用角色執行個體彼此的結束點。

**ConfigurationSettings**  
包含特定角色的功能設定定義。

**憑證**  
包含定義所需的角色的憑證。 在先前的程式碼範例會顯示憑證所用的 Azure 連線設定。

**LocalResources**  
包含定義本機存放區的資源。 本機存放區資源是角色的執行中執行個體的虛擬機器檔案系統上保留的目錄。

**匯入**  
包含定義匯入的模組。 在先前的程式碼範例顯示遠端桌面連線與 Azure 連線的模組。

**啟動**  
包含角色啟動時要執行的工作。 任務是.cmd 或可執行檔文件中所定義。



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
設定您的雲端服務設定會決定**ServiceConfiguration.cscfg**檔案中的值。 您指定您想要部署此檔案中的每一個角色的執行個體數目。 設定的設定在服務定義檔案中所定義的值會新增至 [服務設定檔。 任何雲端服務與相關聯的管理憑證變動也會新增至的檔案。 [Azure 服務設定結構描述 (.cscfg 檔案)](https://msdn.microsoft.com/library/azure/ee758710.aspx)提供服務設定檔案格式。

服務設定檔不會封裝應用程式，但上傳到 Azure 為個別檔案，用來設定雲端服務。 您可以上傳新的服務設定檔，而不用重新部署您的雲端服務。 雲端服務執行時，可以變更雲端服務的設定值。 下列範例顯示設定的設定可定義的網頁和背景工作角色︰

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

您可以參考[服務設定結構描述](https://msdn.microsoft.com/library/azure/ee758710.aspx)，讓您更瞭解下列使用 XML 結構描述，不過，以下是快速說明的項目︰

**執行個體**  
設定執行角色的執行個體的數目。 若要防止您雲端服務可能會變成無法使用升級期間，建議您部署您的網站具角色的多個執行個體。 如此一來，您符合[Azure 計算服務等級協定 (SLA)](http://azure.microsoft.com/support/legal/sla/)，這兩個或多個角色執行個體部署服務時，可確保 99.95%外部連接的網際網路的角色中的指導方針。

**ConfigurationSettings**  
設定的角色的執行個體的設定。 名稱`<Setting>`項目必須符合設定服務定義檔案中的定義。

**憑證**  
設定服務所使用的憑證。 在先前的程式碼範例會示範如何定義遠端存取模組的憑證。 *指紋*屬性的值必須使用設為指紋的憑證。

<p/>

 >[AZURE.NOTE] 憑證指紋可以使用文字編輯器中，新增至 [設定檔的值，也可以新增的 Visual Studio 中的角色的 [**屬性**] 頁面的 [**憑證**] 索引標籤。



## <a name="defining-ports-for-role-instances"></a>定義角色執行個體的連接的埠
Azure 可讓您只有一個項目指向網頁角色。 這表示所有流量都發生透過一個 IP 位址。 您可以設定您的網站以共用檔案的連接埠設定主機標頭，將 [要求以正確的位置。 您也可以設定您的應用程式，以聆聽已知 IP 位址的連接埠。

下列範例顯示網頁角色與網站和 web 應用程式的設定。 網站設定為預設的項目位置連接埠 80，並將 web 應用程式設為接收來自稱為 「 mail.mysite.cloudapp.net 」 的替代主機標頭的要求。

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>變更設定的角色
不含離線服務 Azure 中執行時，您可以更新您的雲端服務的設定。 若要變更的設定資訊，您可以上傳新的設定檔，或編輯位置中的設定檔並將其套用至您執行的服務。 服務的設定，可以進行下列變更︰

- **變更設定的設定值**  
當組態設定變更，角色執行個體可以選擇要套用變更的執行個體處於線上狀態，或資源執行個體正常並套用時的執行個體變更為 [離線時。

- **變更角色執行個體的服務拓撲**  
拓撲變更不會影響執行個體，除了執行個體會被移除。 所有剩餘的執行個體通常不需要回收;不過，您可以選擇資源回收角色執行個體拓撲變更的回應。

- **變更憑證指紋**  
角色執行個體離線時，您僅可以更新憑證。 如果憑證新增、 刪除，或變更角色執行個體處於線上狀態時，Azure 正常會執行個體離線更新憑證，並使其返回上線完成變更後。

### <a name="handling-configuration-changes-with-service-runtime-events"></a>處理服務執行階段事件的設定變更
[Azure 執行階段文件庫](https://msdn.microsoft.com/library/azure/mt419365.aspx)包含[Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx)命名空間，提供類別以從角色的執行個體中執行的程式碼 Azure 環境進行互動。 [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx)類別定義設定變更前後引發下列事件︰

- **[變更](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)事件**  
這是角色的設定變更套用到指定的執行個體，讓您有機會，如有必要下角色執行個體, 進行之前。
- **[變更](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)事件**  
設定變更會套用到指定的執行個體的角色之後發生。

> [AZURE.NOTE] 憑證變更一律離線角色的執行個體，因為他們不會引發 RoleEnvironment.Changing 或 RoleEnvironment.Changed 事件。

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
若要部署 Azure 中的雲端服務應用程式，您必須先封裝應用程式以適當的格式。 若要建立另一種 Visual Studio 的套件檔案，您可以使用**CSPack**命令列工具 （與[Azure SDK](https://azure.microsoft.com/downloads/)安裝）。

**CSPack**會使用服務定義檔和 [服務設定檔的內容來定義套件的內容。 **CSPack**產生的應用程式套件檔案 (.cspkg)，您可以使用上傳到 Azure [Azure 入口網站](cloud-services-how-to-create-deploy-portal.md#create-and-deploy)。 根據預設，名稱為封裝`[ServiceDefinitionFileName].cspkg`，但是您可以藉由指定不同的名稱`/out` **CSPack**] 選項。

**CSPack**是通常位於  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe （在 windows)，請執行的 SDK 與安裝的**Microsoft Azure 命令提示字元**快速鍵。  
>  
>執行 CSPack.exe 程式本身，查看所有可能的選項及命令的相關的文件。

<p />

>[AZURE.TIP]
**Microsoft Azure 計算模擬器**中本機執行您的雲端服務，請使用這個選項複製從他們可以執行計算模擬器中為目錄版面配置的應用程式的二進位檔案**/copyonly**選項。

### <a name="example-command-to-package-a-cloud-service"></a>封裝雲端服務的範例] 命令
下列範例會建立應用程式套件包含網頁角色的資訊。 命令指定使用，何處可以找到二進位檔案，目錄服務定義檔案] 和 [套件檔案的名稱。

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

如果應用程式同時包含網頁角色和工作者角色，則會使用下列命令︰

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

其中變數定義，如下所示︰

| 變數                  | 值 |
| ------------------------- | ----- |
| \[DirectoryName\]         | Azure 專案的.csdef 檔案所在的根專案目錄下子目錄。|
| \[ServiceDefinition\]     | 服務定義檔案的名稱。 根據預設，此檔案名稱為 ServiceDefinition.csdef。  |
| \[OutputFileName\]        | 產生的套件檔案的名稱。 一般而言，這是設定為應用程式的名稱。 如果有不指定任何檔案名稱，則會建立應用程式套件\[ApplicationName\].cspkg。|
| \[角色名稱\]              | 角色服務定義檔案中所定義的名稱。|
| \[RoleBinariesDirectory] | 角色的二進位檔案的位置。|
| \[VirtualPath\]           | 每個服務定義的 [網站] 區段中所定義的虛擬路徑的實體的目錄。|
| \[PhysicalPath\]          | 在 [網站] 節點的服務定義的已定義之每個虛擬路徑內容的實體的目錄。|
| \[RoleAssemblyName\]      | 角色的二進位檔案的名稱。| 


## <a name="next-steps"></a>後續步驟

我建立的雲端服務套件，並想要...

* [設定遠端桌面執行個體雲端服務][remotedesktop]
* [部署雲端服務專案][deploy]

我使用 Visual Studio，且想要...

* [建立新的雲端服務][vs_create]
* [重新設定現有的雲端服務][vs_reconfigure]
* [部署雲端服務專案][vs_deploy]
* [設定遠端桌面執行個體雲端服務][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
