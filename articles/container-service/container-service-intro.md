<properties
   pageTitle="Azure 容器服務簡介 |Microsoft Azure"
   description="Azure 容器服務會提供簡化建立、 設定及管理叢集的虛擬機器執行編應用程式為預先設定的方式。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker，容器，微服務，Mesos、 Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# <a name="azure-container-service-introduction"></a>Azure 容器服務簡介

Azure 容器服務可讓您建立、 設定及管理執行編應用程式為預先設定的虛擬機器叢集的簡單。 它會使用是最佳化的常用的開啟來源排程及協調流程工具設定。 這可讓您使用現有的技巧，或利用大型及成長本文社群專業領域，部署及管理容器型 Microsoft Azure 上的應用程式。


![Azure 容器服務，提供管理編的 Azure 上的多個主機上的應用程式。](./media/acs-intro/acs-cluster.png)


Azure 容器服務會運用 Docker 容器格式，以確保您的應用程式容器會完全可攜式。 它也支援您所選擇的 Marathon 和 DC/OS 或 Docker 廣域，好讓您可以調整數以千計的容器，或甚至數十千分位這些應用程式。

藉由使用 Azure 容器服務，您可以利用 Azure 企業級功能的同時保有應用程式可攜，包括可攜協調流程層級。

<a name="using-azure-container-service"></a>使用容器 Azure 服務
-----------------------------

我們 Azure 容器服務的目標是提供給容器使用開啟來源工具和技術我們的客戶間常用今天主控環境。 為此，我們會為您所選的 orchestrator （亦即/OS 或 Docker 廣域） 公開標準的 API 結束點。 藉由使用這些端點，您可以利用能夠那些端點與交談的任何軟體。 比方說，如果 Docker 廣域端點，您可能會選擇使用 Docker 命令列介面 (CLI)。 DC/OS，您可以選擇使用 DCOS CLI。

<a name="creating-a-docker-cluster-by-using-azure-container-service"></a>建立 Docker 叢集使用 Azure 容器服務
-------------------------------------------------------

若要開始使用 Azure 容器服務，您會部署入口網站 （搜尋 「 Azure 容器服務 」），透過 Azure 容器服務叢集使用 Azure 資源管理員範本 （[Docker 廣域](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)或[DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)） 或[CLI](/documentation/articles/xplat-cli-install/)。 您可以修改提供的快速入門範本包括額外或進階 Azure 設定。 如需有關部署 Azure 容器服務叢集的詳細資訊，請參閱[Azure 容器服務叢集部署](container-service-deployment.md)。

<a name="deploying-an-application"></a>部署應用程式
------------------------

Azure 容器服務，提供選擇 Docker 廣域或 DC/OS 協調流程。 您將應用程式的部署，取決於您所選擇的 orchestrator。

### <a name="using-dcos"></a>使用 DC/作業系統

DC/OS 是根據 Apache Mesos 分散式的系統核心分散式的作業系統。 Apache Mesos 就會放在 Apache 軟體 Foundation 和清單部分[中的最大名稱 IT](http://mesos.apache.org/documentation/latest/powered-by-mesos/)為使用者和參與者。

![設定顯示代理程式與母片的廣域 azure 容器服務。](media/acs-intro/dcos.png)

DC/OS 和 Apache Mesos 包含令人印象深刻的功能集︰

-   經過驗證的延展性

-   容錯複寫母片與從屬使用 Apache 動物園管理員

-   支援 Docker 格式化容器

-   原生隔離 Linux 容器工作

-   Multiresource 排程 （記憶體、 CPU、 磁碟和連接埠）

-   Java Python 與 c + + Api 開發新平行應用程式

-   檢視叢集狀態網頁 UI

根據預設，Azure 容器服務上執行的 DC/OS 包含排程工作負載的 Marathon 協調流程平台。 不過，ACS 的 DC/OS 部署中包含的是服務可以新增您的服務，其中包括火花、 Hadoop、 Cassandra 及更多的 Mesosphere Universe。

![Azure 容器服務中的 DC/OS Universe](media/dcos/universe.png)

#### <a name="using-marathon"></a>使用 Marathon

Marathon 是整個叢集初始化和控制系統中 cgroups，或如果 Azure 容器服務，Docker 格式化容器的服務。 Marathon 提供 web UI，您可以從部署應用程式。 您可以存取此 URL 看起來像`http://DNS_PREFIX.REGION.cloudapp.azure.com`位置 DNS\_首碼] 和 [地區兩者都定義在部署階段。 當然，您也可以提供您自己的 DNS 名稱。 如需有關如何執行使用 Marathon 網頁 UI 容器的詳細資訊，請參閱[容器管理 web ui](container-service-mesos-marathon-ui.md)。

![Marathon 應用程式清單](media/dcos/marathon-applications-list.png)

您也可以使用 REST Api 與 Marathon 進行通訊。 有可供每項工具的用戶端文件庫的數字。 它們涵蓋各種不同的語言，然後當然，您可以使用 HTTP 通訊協定任何語言。 此外，許多熱門的 DevOps 工具提供 Marathon 支援。 當您正在使用 Azure 容器服務叢集，這可以提供作業小組的最大的彈性。 如需有關如何使用 Marathon REST API 來執行容器的詳細資訊，請參閱[以 REST API 容器管理](container-service-mesos-marathon-rest.md)。

### <a name="using-docker-swarm"></a>使用 Docker 廣域

Docker 廣域提供 Docker 的原生叢集。 因為 Docker 廣域做標準 Docker API，任何已與 Docker 精靈通訊的工具就可以使用廣域無障礙縮放 Azure 容器服務上的多個主機。

![Azure 容器服務設定為使用 DC/OS-顯示 jumpbox、 代理程式和母片。](media/acs-intro/acs-swarm2.png)

支援的工具來管理容器廣域叢集上的包含，但不是限於下列︰

-   Dokku

-   Docker CLI 和 Docker 撰寫

-   Krane

-   Jenkins

<a name="videos"></a>視訊
------

開始使用 Azure 容器服務 (101):  

> [AZURE.VIDEO azure-container-service-101]

建置應用程式使用 Azure 容器服務 (建立 2016)

> [AZURE.VIDEO build-2016-building-applications-using-the-azure-container-service]
