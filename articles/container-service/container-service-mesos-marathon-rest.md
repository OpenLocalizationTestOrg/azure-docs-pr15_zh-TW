<properties
   pageTitle="REST API 透過 azure 容器服務容器管理 |Microsoft Azure"
   description="使用 Marathon REST API Azure 容器服務 Mesos 叢集部署容器。"
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

# <a name="container-management-through-the-rest-api"></a>透過 REST API 容器管理

DC/OS 提供的部署及縮放叢集的負載，同時基礎硬體環境。 DC/OS，上方有管理排程和執行計算工作負載的架構。

雖然架構可供許多熱門的工作量，這份文件會說明如何建立及使用 Marathon 縮放容器部署。 您必須要有 DC/OS 叢集 Azure 容器服務中設定才能使用這些範例。 您也需要遠端連線到此叢集。 如需有關這些項目的詳細資訊，請參閱下列文章︰

- [部署 Azure 容器服務叢集](container-service-deployment.md)
- [連接到 Azure 容器服務叢集](container-service-connect.md)

您已連線到 Azure 容器服務叢集之後，您可以存取的 DC/OS 及相關的 REST Api 透過 http://localhost:local 連接埠。 這份文件中的範例，假設您要設定通道連接埠 80。 例如，Marathon 端點可以連線到`http://localhost/marathon/v2/`。 如需各種 Api 的詳細資訊，請參閱[Marathon API](https://mesosphere.github.io/marathon/docs/rest-api.html)和[Chronos API](https://mesos.github.io/chronos/docs/api.html)，Mesosphere 文件和[Mesos 排程器 API](http://mesos.apache.org/documentation/latest/scheduler-http-api/)Apache 文件。

## <a name="gather-information-from-dcos-and-marathon"></a>從 DC/OS 和 Marathon 收集資訊

在 DC/OS 叢集部署容器之前，收集的一些 DC/OS 叢集，例如名稱和目前的狀態，亦即/OS 代理程式的相關資訊。 若要這麼做，查詢`master/slaves`DC/OS REST API 的結束點。 一切順利，您會看到每個的 DC/OS 代理程式和數個屬性的清單。

```bash
curl http://localhost/mesos/master/slaves
```

現在，使用 [Marathon`/apps`檢查目前的應用程式部署到 DC/OS 叢集的結束點。 如果這是新的叢集，您會看到空白的陣列的應用程式。

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>部署 Docker 格式化容器

您可以使用 JSON 檔案描述預定的部署，以部署 Docker 格式化容器，透過 Marathon。 下列範例會部署 Nginx 容器繫結連接埠 80 的連接埠 80 容器的 DC/OS 代理程式。 也請注意，'acceptedResourceRoles'] 屬性設定為 「 slave_public 」。 這會將容器部署在-在公開代理程式縮放比例設定代理程式。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

部署 Docker 格式化容器，才能建立 JSON 檔案，或使用在[Azure 容器服務示範](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)提供的範例。 將其儲存在可存取的位置。 接下來，若要部署容器，請執行下列命令。 指定 JSON 檔案的名稱。

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

輸出會類似下列動作︰

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

現在，如果您查詢 Marathon 應用程式，這個新的應用程式會顯示在輸出中。

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>不按比例縮放您容器

您也可以使用 Marathon API 擴展或不按比例縮放的應用程式部署中。 在上一個範例中，您可以部署應用程式的執行個體之一。 現在就讓我們擴充這個出三個應用程式執行個體。 若要這麼做，請使用下列 JSON 文字，以建立 JSON 檔案並將其儲存在可存取的位置。

```json
{ "instances": 3 }
```

執行下列命令以擴展應用程式。

>[AZURE.NOTE] URI 會 http://localhost/marathon/v2/apps/ 然後要不按比例縮放的應用程式的識別碼。 如果您使用的提供以下，URI 應 http://localhost/marathon/v2/apps/nginx Nginx 範例。

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

最後，查詢 Marathon 端點的應用程式。 您會看到，現在有三種 Nginx 容器。

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>使用 PowerShell 將這個練習︰ 使用 PowerShell Marathon REST API 互動

在 Windows 系統使用 PowerShell 命令，您可以執行相同的動作。

若要收集資訊的 DC/OS 叢集，例如代理程式的名稱和代理程式狀態，執行下列命令。

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

您可以使用 JSON 檔案描述預定的部署，以部署 Docker 格式化容器，透過 Marathon。 下列範例會部署 Nginx 容器繫結連接埠 80 的連接埠 80 容器的 DC/OS 代理程式。

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

建立您自己 JSON 的檔案，或使用在[Azure 容器服務示範](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json)提供的範例。 將其儲存在可存取的位置。 接下來，若要部署容器，請執行下列命令。 指定 JSON 檔案的名稱。

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

您也可以使用 Marathon API 擴展或不按比例縮放的應用程式部署中。 在上一個範例中，您可以部署應用程式的執行個體之一。 現在就讓我們擴充這個出三個應用程式執行個體。 若要這麼做，請使用下列 JSON 文字，以建立 JSON 檔案並將其儲存在可存取的位置。

```json
{ "instances": 3 }
```

執行下列命令以擴展應用程式。

> [AZURE.NOTE] URI 會 http://localhost/marathon/v2/apps/ 然後要不按比例縮放的應用程式的識別碼。 如果您使用的 Nginx 樣本提供以下，URI 就是 http://localhost/marathon/v2/apps/nginx。

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>後續步驟

- [閱讀更多關於 Mesos HTTP 結束點]( http://mesos.apache.org/documentation/latest/endpoints/)。
- [閱讀更多關於 Marathon REST API]( https://mesosphere.github.io/marathon/docs/rest-api.html)。
