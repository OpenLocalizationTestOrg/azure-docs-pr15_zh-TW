<properties
   pageTitle="Docker 及撰寫虛擬機器 |Microsoft Azure"
   description="撰寫與 Docker 處理 Linux 虛擬機器 Azure 中的快速簡介"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>開始使用 Docker 與撰寫定義和 Azure 虛擬機器上執行多容器的應用程式

開始使用 Docker 和[撰寫](http://github.com/docker/compose)來定義和 Linux 中 Azure 虛擬機器上執行複雜的應用程式。 與撰寫，您可以使用簡單的文字檔案以定義包含多個 Docker 容器的應用程式。 您然後微調應用程式中的所有部署環境定義單一命令。 

例如，本文將示範如何快速設定 WordPress 部落格的後端上 Ubuntu VM MariaDB SQL 資料庫。 您也可以使用撰寫設定更複雜的應用程式。


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>步驟 1︰ 設定為 Docker 主機 Linux VM

您可以使用 [在 Azure Marketplace 各種 Azure 程序並提供的圖像或資源管理員範本，建立 Linux VM，並將其設定為 Docker 主機。 例如，請參閱[使用 Docker VM 延伸部署您的環境](virtual-machines-linux-dockerextension.md)，迅速建立 Ubuntu VM Azure Docker VM 副檔名使用[範本快速入門](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu)。 

當您使用 Docker VM 副檔名時，您 VM 自動設定為 Docker 主機和撰寫已預先安裝。 文件中的範例示範如何使用[for Mac、 Linux 和 Windows Azure 命令列介面](../xplat-cli-install.md)(Azure CLI) 若要建立 VM 資源管理員模式。

從先前的文件的 [基本] 命令會建立資源群組名稱`myResourceGroup`中`West US`位置及部署 VM 安裝 Azure Docker VM 副檔名為︰

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>步驟 2︰ 確認已安裝撰寫

在部署完成時，您使用 DNS 的新 Docker 主機 SSH 命名所提供部署。 您可以使用`azure vm show -g myDockerResourceGroup -n myDockerVM`若要檢視您的 VM，包括 DNS 名稱的詳細資料。

若要查看撰寫已安裝 VM 上，執行下列命令︰

```bash
docker-compose --version
```

您會看到類似的輸出`docker-compose 1.6.2, build 4d72027`。

>[AZURE.TIP] 如果您用來建立 Docker 主機需要安裝撰寫您自己的另一種方法，請參閱[撰寫文件](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md)。


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>步驟 3︰ 建立 docker compose.yml 的設定檔

接下來您建立`docker-compose.yml`檔案，只是一個文字設定檔案，以定義 Docker 容器 VM 上執行。 檔案指定的圖像，在每個容器上執行 （或可能是從 Dockerfile 組建），所需的環境變數和相依性、 連接埠、 和容器之間的連結。 如需 yml 檔案語法的詳細資訊，請參閱[撰寫檔案參考](http://docs.docker.com/compose/yml/)。

建立`docker-compose.yml`檔案，如下所示︰

```bash
touch docker-compose.yml
```

使用您最愛的文字編輯器來新增一些資料檔案。 下列範例會使用`vi`編輯器︰

```bash
vi docker-compose.yml
```

下列範例貼到您的文字檔中。 此設定會使用圖像[DockerHub 登錄](https://registry.hub.docker.com/_/wordpress/)安裝 WordPress （開啟來源部落格和內容管理系統），並連結的後端 MariaDB SQL 資料庫。 輸入您自己`MYSQL_ROOT_PASSWORD`，如下所示︰

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>步驟 4︰ 開始撰寫容器

在相同的目錄中您`docker-compose.yml`檔案中，執行下列命令 (根據您的環境中，您可能需要執行`docker-compose`使用`sudo`。):

```bash
docker-compose up -d

```

這個命令會啟動中指定的 Docker 容器`docker-compose.yml`。 需要一兩分鐘的時間才能完成這個步驟。 您會看到類似以下的範例輸出︰

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] 請務必使用**-d**選項啟動時，讓容器在背景中執行持續的時間。

若要確認設定容器，輸入`docker-compose ps`。 您應該會看到類似︰

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

您現在可以連線至 WordPress 直接在連接埠 80 VM 上。 開啟網頁瀏覽器，然後輸入您 VM DNS 名稱 (例如`http://myresourcegroup.westus.cloudapp.azure.com`)。 您現在應該會看到 WordPress 開始] 畫面中，您可以完成安裝及開始使用應用程式的位置。

![WordPress [開始] 畫面][wordpress_start]


## <a name="next-steps"></a>後續步驟

* 移至 [更多選項，設定您的 Docker VM Docker 及撰寫[Docker VM 副檔名使用者指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md)。 例如，其中一個選項是放置撰寫 yml 檔案 （轉換為 JSON） 直接在 Docker VM 副檔名的設定。
* 請參閱[撰寫命令列參考](http://docs.docker.com/compose/reference/)和[使用者指南](http://docs.docker.com/compose/)建立及部署多容器應用程式的更多範例。
* 請使用 Azure 資源管理員範本，您自己或其中一個提供[社群](https://azure.microsoft.com/documentation/templates/)部署 Docker 與應用程式設定撰寫 Azure VM。 例如，[部署與 Docker WordPress 部落格](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql)範本會使用 Docker 及撰寫快速部署與 MySQL 後端 Ubuntu VM 上的 [WordPress。
* 請嘗試整合[Docker 廣域](virtual-machines-linux-docker-swarm.md)叢集 Docker 撰寫。 請參閱[使用撰寫與廣域](https://docs.docker.com/compose/swarm/)案例。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png
