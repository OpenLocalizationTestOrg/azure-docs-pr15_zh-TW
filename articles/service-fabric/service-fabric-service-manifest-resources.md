<properties
   pageTitle="指定服務布料的轉印圖樣服務端點 |Microsoft Azure"
   description="如何描述端點資源中的服務的資訊，包括如何設定 HTTPS 端點"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="specify-resources-in-a-service-manifest"></a>指定服務資訊清單中的資源

## <a name="overview"></a>概觀

服務資訊清單可讓要宣告/變更而不變更編譯程式碼服務所使用的資源。 Azure 服務布料的轉印圖樣服務的支援結束點資源的設定。 透過應用程式顯示在 SecurityGroup，同時能控制的存取權的服務資訊清單中所指定的資源。 資源的宣告可讓這些資源，變更部署時，這表示服務不需要以介紹新的設定機制。 結構描述定義 ServiceManifest.xml 檔案已安裝的服務布料的轉印圖樣 SDK 和*C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*工具。

## <a name="endpoints"></a>結束點

當端點資源服務清單中所定義時，服務布料的轉印圖樣會指派連接埠從保留的應用程式的連接埠範圍時未明確指定連接埠。 例如，查看*ServiceEndpoint1*資訊清單的程式碼片段提供此段落之後中指定的端點。 此外，服務也可以要求資源中的特定連接埠。 同時執行相同的節點的服務的複本共用連接埠，不同的叢集節點上執行的服務複本可以指派不同的連接埠號碼。 服務複本可以使用下列連接埠視需要複寫和接聽用戶端要求。

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

若要閱讀更多關於從 config 封裝設定參考端點檔案 (settings.xml)，請參閱以[設定狀態可靠的服務](service-fabric-reliable-services-configuration.md)。

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>範例︰ 指定 HTTP 服務端點

下列服務資訊清單定義一個 TCP 端點資源和中的兩個 HTTP 端點資源&lt;資源&gt;項目。

HTTP 結束點就會自動 ACL 會依服務布料的轉印圖樣。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>範例︰ 指定的 HTTPS 端點服務

HTTPS 通訊協定提供伺服器驗證，而且也可用於加密用戶端與伺服器通訊。 若要啟用 HTTPS 您服務布料的轉印圖樣的服務，指定的通訊協定*資源]-> [結束點]-> [結束點*服務資訊清單，如先前所示的端點*ServiceEndpoint3*一節。

>[AZURE.NOTE] 服務的通訊協定無法變更應用程式升級時，不將其從屬相關變更。


以下是您需要為 HTTPS 的 ApplicationManifest 範例。 必須提供您的憑證指紋。 EndpointRef 是 EndpointResource ServiceManifest，您將設定 HTTPS 通訊協定中的參照。 您可以新增多個 EndpointCertificate。  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
