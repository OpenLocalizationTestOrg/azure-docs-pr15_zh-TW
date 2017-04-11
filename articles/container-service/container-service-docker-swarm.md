<properties
   pageTitle="Azure 容器服務容器管理 Docker 廣域 |Microsoft Azure"
   description="部署至 Docker 廣域 Azure 容器服務中的 [容器"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker，容器，微服務，Mesos、 Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-with-docker-swarm"></a>使用 Docker 廣域容器管理

Docker 廣域提供一組集區式 Docker 主機部署編工作負載的環境。 Docker 廣域會使用原生 Docker API。 管理上 Docker 廣域容器的工作流程是幾乎相同，它會是單一容器主機上。 這份文件提供部署中的 Docker 廣域 Azure 容器服務執行個體的編工作負載的簡單的範例。 如需更多在 Docker 廣域深入文件，請參閱[Docker 廣域 Docker.com 上](https://docs.docker.com/swarm/)。

這份文件中的先決條件︰

[Azure 容器服務中建立廣域叢集](container-service-deployment.md)

[與廣域叢集 Azure 容器服務的連線](container-service-connect.md)

## <a name="deploy-a-new-container"></a>部署新的容器

若要建立新的容器 Docker 廣域中使用`docker run`（確保您已開啟的母片，依照上述的必要條件 SSH 通道） 的命令。 此範例會建立從容器`yeasy/simple-web`圖像︰


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

已建立容器之後，請使用`docker ps`傳回容器的資訊。 請注意以下廣域代理程式裝載容器會列出︰


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

您現在可以存取公用 DNS 名稱廣域代理程式負載平衡器透過容器中執行的應用程式。 Azure 入口網站中，您可以找到這項資訊︰  


![實數造訪結果](media/real-visit.jpg)  

依預設負載平衡器有連接埠 80、 8080 和 443 開啟。 如果您想要連線到另一個您要開啟的連接埠 Azure 負載平衡器上代理程式集區的連接埠。

## <a name="deploy-multiple-containers"></a>部署多個容器

為多個容器會啟動，藉由執行 「 docker 執行 「 多次，您可以使用`docker ps`命令，以查看其裝載容器上執行。 在下面的範例中，三個容器平均分配跨三個廣域代理程式︰  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>使用 Docker 撰寫部署容器

您可以使用 Docker 撰寫自動化的部署及多個容器的設定。 若要這麼做，請確定已建立安全命令介面 (SSH) 通道，並已設定 （請參閱上述的必要條件） 的 DOCKER_HOST 變數。

建立您的本機系統 docker compose.yml 檔案。 若要這麼做，請使用此[範例](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml)。

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

執行`docker-compose up -d`開始容器部署︰


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

最後，將會傳回執行容器的清單。 清單中會顯示已部署使用 Docker 撰寫容器︰


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

當然，您可以使用`docker-compose ps`檢查只在中定義的容器您`compose.yml`檔案。

## <a name="next-steps"></a>後續步驟

[深入瞭解 Docker 廣域](https://docs.docker.com/swarm/)
