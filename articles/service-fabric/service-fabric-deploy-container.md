<properties
   pageTitle="服務布料的轉印圖樣及部署容器 |Microsoft Azure"
   description="服務布料的轉印圖樣及使用容器的部署 microservice 應用程式。 本文將說明服務架構提供容器的功能，以及如何將叢集部署 Windows 容器影像"
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
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>預覽︰ 將 Windows 容器部署至服務布料的轉印圖樣

> [AZURE.SELECTOR]
- [部署 Windows 容器](service-fabric-deploy-container.md)
- [部署 Docker 容器](service-fabric-deploy-container-linux.md)

本文會引導您建置編的 Windows 容器中的服務。 

>[AZURE.NOTE] 此功能位於預覽 Linux 和目前不適用於 Windows Server 2016 搭配使用。 之後，就會提供服務布料的轉印圖樣的下一個版本的 Windows Server 2016 預覽和支援在後續發行中取得。

服務布料的轉印圖樣有數種容器功能可協助您建置組成的應用程式的 microservices 的是編。 這些稱為編的服務。 

功能包括︰

- 容器圖像部署和啟動
- 資源管理
- 存放庫驗證
- Host （主機） 連接埠對應至容器連接埠
- 容器的容器探索及通訊
- 若要設定，並設定環境變數的功能

可讓查看每個功能依序時包裝到應用程式中所包含的編的服務。

## <a name="packaging-a-windows-container"></a>包裝 Windows 容器

當包裝容器，您可以選擇要使用 Visual Studio 專案範本或[以手動方式建立應用程式套件](#manually)。 使用 Visual Studio，應用程式套件結構及資訊清單的檔案，會建立新的專案精靈，您 （即將下一個版本）。

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>使用 Visual Studio 封裝現有的容器圖像

>[AZURE.NOTE] 在 Visual Studio 工具 SDK 未來版本中，您可以新增至應用程式的容器類似的方式，您可以立即新增來賓可執行。 請參閱[部署至服務布料的轉印圖樣可執行以來賓](service-fabric-deploy-existing-app.md)的主題。 目前您必須執行手動包裝，如下所述。

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>手動封裝和部署容器
手動封裝編的服務程序會根據下列步驟︰

1. 若要將存放庫發佈容器。
2. 建立套件目錄結構。
3. 編輯服務資訊清單檔案。
4. 編輯應用程式資訊清單檔案。

## <a name="container-image-deployment-and-activation"></a>容器圖像部署和啟動。
服務布料的轉印圖樣[應用程式模型](service-fabric-application-model.md)] 中，容器代表多個服務的複本會放在其中主應用程式。 若要部署，並啟動容器，將 [容器圖像的名稱`ContainerHost`服務資訊清單中的項目。

服務資訊清單中新增`ContainerHost`項目的點，並設定`ImageName`容器存放庫和圖像的名稱。 下列部分資訊清單顯示部署從稱為*myrepo*存放庫呼叫*myimage:v1*容器的範例

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

您可以藉由指定選擇性提供輸入的命令至容器圖像`Commands`項目，並以逗號分隔設定要執行容器內的命令。 

## <a name="resource-governance"></a>資源管理
資源管理容器的功能，而限制可以使用 [主機的 [容器的資源。 `ResourceGovernancePolicy`，指定應用程式資訊清單中，可讓您將服務代碼套件的資源限制。 可設定資源限制

- 記憶體
- MemorySwap
- CpuShares （CPU 相對粗細）
- MemoryReservationInMB  
- BlkioWeight （BlockIO 相對粗細）。 

>[AZURE.NOTE] 在未來的版本，支援指定特定區塊 IO 限制，例如 IOPs，BPS 和其他人會可能的讀/寫。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>存放庫驗證
若要下載的容器，您可能要提供登入認證，以容器存放庫。 登入認證，*應用程式*資訊清單中指定用來指定登入資訊或 SSH 鍵，從圖像存放庫下載容器圖像。  下列範例會顯示稱為*TestUser*以及密碼，以純文字的帳戶。 這**不**建議使用。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

密碼可以並應使用部署至電腦的憑證來加密。

下列範例會顯示稱為*TestUser*使用稱為*MyCert*的憑證來加密的密碼的帳戶。 您可以使用`Invoke-ServiceFabricEncryptText`Powershell 命令建立密碼的私人密碼文字。 請參閱本文[管理服務布料的轉印圖樣應用程式中的機密資料](service-fabric-application-secret-management.md)的詳細資料。 私密金鑰的憑證解密密碼，必須先部署到-頻方法 （在這是透過 ARM Azure) 中的本機電腦。 時服務布料的轉印圖樣有服務套件部署到電腦，, 則無法解密密碼和帳戶名稱，以及驗證方法使用這些認證容器存放庫。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Host （主機） 連接埠對應至容器連接埠
您可以設定用來指定通訊與容器主機連接埠`PortBinding`應用程式資訊清單中。 連接埠繫結地圖服務接聽主機上的連接埠容器內的連接埠。


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>容器的容器探索及通訊
使用`PortBinding`原則，您可以對應至容器連接埠`Endpoint`在服務資訊清單，如下列範例所示。 端點`Endpoint1`可以指定固定的連接埠，例如連接埠 80 或指定沒有連接埠，為您在此情況下選擇從叢集的應用程式的連接埠範圍隨機連接埠。

來賓容器，指定的`Endpoint`等服務資訊清單中的這可讓服務，自動將此端點發佈至命名服務，以便在叢集執行其他服務才能探索使用解決服務其餘查詢此容器布料的轉印圖樣。 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

註冊命名服務，您可以輕鬆地使用[反向 proxy](service-fabric-reverseproxy.md)您容器中執行程式碼中的容器至容器通訊。 您只需要是提供反向 proxy http 聆聽連接埠] 和 [您要設定這些環境變數為通訊的服務的名稱。 如何執行此動作，請參閱下一節。  

## <a name="configure-and-set-environment-variables"></a>設定並設定環境變數
環境變數可指定的對手服務中的每個程式碼套件資訊清單的兩種部署容器中或程序/來賓可執行檔的服務。 可以特別中應用程式顯示覆寫這些環境變數值，或指定期間的部署與應用程式參數。

下列服務資訊清單 XML 程式碼片段顯示如何指定環境變數，程式碼套件的範例。 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

可覆寫這些環境變數，應用程式資訊清單層級︰

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

在上述範例中，我們已指定的值是明確`HttpGateway`環境變數 (19000) 的值時`BackendServiceName`參數透過設定`[BackendSvc]`應用程式參數。 這可讓您指定的值`BackendServiceName`值部署應用程式時，資訊清單中沒有固定的值。 

## <a name="complete-examples-for-application-and-service-manifest"></a>完成應用程式和服務資訊清單的範例
以下是範例應用程式資訊顯示的容器功能。


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


以下是範例服務資訊清單 （指定的上一個應用程式顯示），以顯示容器功能

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>
