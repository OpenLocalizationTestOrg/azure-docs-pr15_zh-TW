<properties
   pageTitle="公用與私人 DC/OS 代理程式集區 ACS |Microsoft Azure"
   description="公用與私人代理程式集區運作方式與 Azure 容器服務叢集。"
   services="container-service"
   documentationCenter=""
   authors="Thraka"
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
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure 容器服務的 DC/OS 代理程式集區

DC/OS Azure 容器服務會將公用或私用集區將代理程式。 部署使用者可對會影響您的容器服務在電腦之間的協助工具的 [資料庫。 電腦可以 （公開） 網際網路上公開，或保留內部 （私人）。 本文提供的簡介，原因有公開及私密金鑰的資料庫。

### <a name="private-agents"></a>私人代理程式

私人代理程式節點執行透過非舉例來說網路。 從 [管理員] 區域，或透過公用區域邊緣路由器，才可存取這個網路。 根據預設，亦即/OS 會啟動私人代理程式節點上的應用程式。 如需網路安全性相關資訊，請參考[DC/OS 文件](https://dcos.io/docs/1.7/administration/securing-your-cluster/)。

### <a name="public-agents"></a>公用代理程式

公用代理程式節點透過公開存取網路中執行 OS DC/應用程式與服務。 如需網路安全性相關資訊，請參考[DC/OS 文件](https://dcos.io/docs/1.7/administration/securing-your-cluster/)。

## <a name="using-agent-pools"></a>使用代理程式集區

根據預設， **Marathon**部署至*私人*代理程式節點任何新的應用程式。 您必須明確部署至*公用*節點期間的應用程式建立的應用程式。 選取 [**選項**] 索引標籤，然後輸入**slave_public** **接受資源角色**值。 此程序會說明[以下](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)和[DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/)文件中。

## <a name="next-steps"></a>後續步驟

請閱讀[管理您的 DC/OS 容器](container-service-mesos-marathon-ui.md)的詳細資訊。

瞭解如何[開啟防火牆](container-service-enable-public-access.md)提供 Azure 允許公用存取您的 DC/OS 容器。