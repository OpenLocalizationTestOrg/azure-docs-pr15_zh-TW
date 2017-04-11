<properties
   pageTitle="服務布料的轉印圖樣專案建立下一個步驟 |Microsoft Azure"
   description="本文包含的服務布料的轉印圖樣的核心開發工作的一組的連結"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="your-service-fabric-application-and-next-steps"></a>您的服務布料的轉印圖樣應用程式和下一個步驟
已建立應用程式 Azure 服務布料的轉印圖樣。 本文將說明專案及一些可能的下一個步驟的架構。

## <a name="your-application"></a>您的應用程式
每個新的應用程式中包含的應用程式專案。 可能會有一或兩個其他專案，請視服務選擇的類型而定。

### <a name="the-application-project"></a>應用程式專案
應用程式專案所組成︰

- 參照到的應用程式服務的一組。

- 兩個發佈設定檔 （本機和雲端），您可以使用維護的使用不同的環境，例如叢集端點，以及是否要執行升級部署預設相關的喜好設定喜好設定]。

- 兩個應用程式參數檔案 （本機和雲端） 若要維持環境專屬的應用程式的設定，例如數字的磁碟分割區以建立的服務，您可以使用。

- 您可以使用部署應用程式從命令列或一部分自動化的連續整合和部署管線部署指令碼。

- 應用程式資訊清單，將應用程式的說明。 您可以尋找資訊清單 ApplicationPackageRoot 資料夾下。

### <a name="stateless-service"></a>無狀態服務
當您新增新的狀態服務時，Visual Studio 會將服務專案加入至您的方案包含撈類型`StatelessService`。 服務遞增計數器區域變數。

### <a name="stateful-service"></a>設定狀態服務
當您新增新的狀態服務時，Visual Studio 會將服務專案加入至您的方案包含撈類型`StatefulService`。 服務累加在其`RunAsync`方法，並將結果儲存`ReliableDictionary`。

### <a name="actor-service"></a>動作項目服務
當您新增新的可靠動作項目時，Visual Studio 會將兩個專案加入至您的方案︰ 動作項目專案和介面專案。

動作項目專案提供的設定方法和動作項目的狀態中可靠保存快速計數器的值。 介面 project 提供的介面，其他服務可用來呼叫動作項目。

### <a name="stateless-web-api"></a>無網路 API
無 Web API project 提供可用來開啟您的外部用戶端的應用程式的基本 web 服務。 如需有關如何專案結構化，請參閱[使用 OWIN 自我裝載的服務布料的轉印圖樣 Web API 服務](service-fabric-reliable-services-communication-webapi.md)。

### <a name="aspnet-core"></a>ASP.NET 核心

服務布料的轉印圖樣 SDK 提供相同的 ASP.NET 核心一組可供獨立 ASP.NET 核心專案範本︰ 清空，[網頁 API][aspnet-webapi]，和[Web 應用程式][aspnet-webapp]。

## <a name="next-steps"></a>後續步驟
### <a name="create-an-azure-cluster"></a>建立 Azure 叢集
服務布料的轉印圖樣 SDK 提供本機叢集開發和測試。 若要建立叢集 Azure 中，請參閱[設定服務布料的轉印圖樣叢集從 Azure 入口網站][create-cluster-in-portal]。

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>請嘗試免費的廠商叢集部署 Azure

如果您想要嘗試使用沒有設定您自己的叢集 Azure 中的部署及管理應用程式，您可以使用免費的[廠商叢集服務](http://aka.ms/tryservicefabric)。

### <a name="publish-your-application-to-azure"></a>發佈至 Azure 應用程式
您可以發佈您的應用程式直接從 Visual Studio Azure 叢集。 若要瞭解作法，請參閱[發佈您的應用程式 Azure][publish-app-to-azure]。

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>使用服務布料的轉印圖樣檔案總管，以視覺化方式呈現叢集
服務布料的轉印圖樣 Explorer 提供容易以視覺化方式呈現叢集，包括部署的應用程式和實際的版面配置。 若要深入瞭解，請參閱[視覺化使用服務布料的轉印圖樣總管叢集][visualize-with-sfx]。

### <a name="version-and-upgrade-your-services"></a>版本與升級您的服務
獨立的版本設定與升級的獨立服務應用程式中，可讓服務布料的轉印圖樣。 若要深入瞭解，請參閱[版本設定與升級您的服務][app-upgrade-tutorial]。

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>設定 Visual Studio 小組服務連續整合
若要瞭解如何您可以設定持續整合程序應用程式服務布料的轉印圖樣，請參閱[設定與 Visual Studio 小組服務的連續整合][ci-with-vso]。


<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
