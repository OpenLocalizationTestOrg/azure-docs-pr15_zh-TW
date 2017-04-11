<properties
   pageTitle="部署使用 MongoDB Node.js 應用程式 |Microsoft Azure"
   description="部署至 Azure 服務布料的轉印圖樣叢集的逐步解說如何封裝多個來賓可執行檔"
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
   ms.date="10/22/2016"
   ms.author="msfussell;mikhegn"/>


# <a name="deploy-multiple-guest-executables"></a>部署多個來賓可執行檔

本文將示範如何封裝並將多個來賓可執行檔部署至 Azure 服務布料的轉印圖樣。 建立並部署單一服務布料的轉印圖樣封裝讀[部署至服務布料的轉印圖樣可執行以來賓](service-fabric-deploy-existing-app.md)的方式。

此逐步解說會顯示如何部署與資料存放區為使用 MongoDB Node.js 前端應用程式，您可以在另一個應用程式中有相依性的任何應用程式套用步驟。   

您可以使用 Visual Studio 來產生包含多個來賓可執行檔的應用程式套件。 請參閱[使用 Visual Studio 封裝現有的應用程式](service-fabric-deploy-existing-app.md#using-visual-studio-to-package-an-existing-executable)。 您已新增第一個來賓可執行檔之後，以滑鼠右鍵按一下該應用程式專案，然後選取**新增]-> [新增服務布料的轉印圖樣服務**方案中加入第二個來賓可執行專案。 注意︰ 如果您選擇要連結的來源 Visual Studio 專案，建置 Visual Studio 解決方案中，將請務必應用程式套件最新的來源的變更。 

## <a name="manually-package-the-multiple-guest-executable-application"></a>手動封裝多個來賓可執行應用程式
或者您可以手動封裝執行來賓。 手動包裝，本文會使用 [服務布料的轉印圖樣包裝工具，可在[http://aka.ms/servicefabricpacktool](http://aka.ms/servicefabricpacktool)。

### <a name="packaging-the-nodejs-application"></a>包裝 Node.js 應用程式
本文假設是在服務布料的轉印圖樣叢集節點未安裝 Node.js。 因此，您需要將 Node.exe 新增到您之前包裝的節點應用程式的根目錄。 目錄 Node.js 應用程式結構 （使用 Express web 架構和碧眼範本引擎） 看起來應該像如下︰

```
|-- NodeApplication
  	|-- bin
        |-- www
  	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
  	|-- public
        |-- images
        |-- etc.
  	|-- routes
        |-- index.js
        |-- users.js
  	|-- views
        |-- index.jade
        |-- etc.
  	|-- app.js
  	|-- package.json
  	|-- node.exe
```

下一個步驟中，為您建立的 Node.js 應用程式的應用程式套件。 下列程式碼會建立包含 Node.js 應用程式服務布料的轉印圖樣應用程式套件。

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

以下是使用參數的說明︰

- **/source**指向應封裝的應用程式目錄。
- **/target**定義所建立的套件的目錄。 此目錄具有不同來源目錄。
- **/appname**定義現有的應用程式的應用程式的名稱。 請務必瞭解此翻譯服務名稱中的資訊，而非服務布料的轉印圖樣應用程式的名稱。
- **/exe**定義服務布料的轉印圖樣應該會在此情況下啟動的可執行檔`node.exe`。
- **/ma**定義用來啟動可執行檔的引數。 如未安裝 Node.js，需要啟動 Node.js 網頁伺服器，藉由執行服務布料的轉印圖樣`node.exe bin/www`。  `/ma:'bin/www'`告訴包裝工具使用`bin/ma`做為 node.exe 引數。
- **/AppType**定義服務布料的轉印圖樣應用程式類型的名稱。

如果您瀏覽至皆中所指定的目錄，您可以看見工具所建立的完整的服務布料的轉印圖樣套件，如下所示︰

```
|--[yourtargetdirectory]
  	|-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
產生的 ServiceManifest.xml 現在有一個區段，將說明如何應啟動 Node.js 網頁伺服器，程式碼片段之以下所示︰

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
在此範例中，Node.js 網頁伺服器接聽連接埠 3000，因此您需要更新 ServiceManifest.xml 檔案，如下所示的結束點資訊。   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>包裝 MongoDB 應用程式
現在，您有封裝 Node.js 應用程式，您可以繼續並封裝 MongoDB。 如之前所述，無法 Node.js 和 MongoDB 特定現在幫助的步驟。 事實上，它們會套用至所有是要放在一起封裝為一個服務布料的轉印圖樣應用程式的應用程式。  

若要封裝 MongoDB，您想要確定您封裝 Mongod.exe 和 Mongo.exe。 兩個二進位檔案位於`bin`MongoDB 安裝目錄的目錄。 目錄結構看起來就像以下項目。

```
|-- MongoDB
  	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
服務布料的轉印圖樣必須開始 MongoDB 命令類似下面，因此您需要使用`/ma`參數時包裝 MongoDB。

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] 不會被資料保留在節點失敗，如果您在本機節點的目錄中放置 MongoDB 資料目錄。 您應該使用長期儲存空間或實作設定，以避免資料遺失 MongoDB 複本。  

在 PowerShell 或命令殼層，我們可以執行包裝工具使用下列參數︰

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

若要新增至您的服務布料的轉印圖樣應用程式套件的 MongoDB，您需要確認 /target 參數會指向相同的目錄已包含應用程式的資訊清單，以及 Node.js 應用程式。 您也需要請確認您使用的 ApplicationType 的名稱。

讓我們來瀏覽的目錄，並檢查工具所建立。

```
|--[yourtargetdirectory]
  	|-- MyNodeApplication
  	|-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
  	|-- ApplicationManifest.xml
```
如您所見，工具就會新增新的資料夾，MongoDB，包含 MongoDB 二進位檔案的目錄。 如果您開啟`ApplicationManifest.xml`檔案，您可以看到套件現在包含 Node.js 應用程式和 MongoDB。 下列程式碼會顯示的應用程式顯示的內容。

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>發佈應用程式
最後一個步驟是發行到本機服務布料的轉印圖樣叢集應用程式，請使用下列 PowerShell 指令碼︰

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

一旦到本機叢集成功發佈應用程式，您可以存取 Node.js 上的應用程式，我們輸入服務資訊清單的 Node.js 應用程式，例如 http://localhost:3000 中的連接埠。

在此教學課程中，您已經知道如何輕鬆地封裝為一個服務布料的轉印圖樣應用程式的兩個現有的應用程式。 您也必須瞭解如何將其部署到服務布料的轉印圖樣，以便能有助於服務布料的轉印圖樣功能，例如高可用性和健康情況系統整合。

## <a name="next-steps"></a>後續步驟

- 深入瞭解部署容器與[服務布料的轉印圖樣與容器的概觀](service-fabric-containers-overview.md)
