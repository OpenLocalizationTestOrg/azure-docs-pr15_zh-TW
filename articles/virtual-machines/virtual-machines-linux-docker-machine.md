<properties
    pageTitle="Azure Docker 電腦中建立 Docker 主機 |Microsoft Azure"
    description="說明 Docker Machine 以建立 docker 主機 Azure 中使用。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="rasquill"/>

# <a name="use-docker-machine-with-the-azure-driver"></a>使用 Docker 機器 Azure 驅動程式

[Docker](https://www.docker.com/)是其中一個最常用的隔離應用程式的資料，並計算共用資源的方式使用 Linux 容器，而不是虛擬機器的虛擬化方法。 本主題說明何時及如何使用[Docker 機器](https://docs.docker.com/machine/)( `docker-machine` ] 命令) 來建立新的 Linux Vm 啟用為您 Linux 容器的 docker 主機 Azure 中。


## <a name="create-vms-with-docker-machine"></a>建立 Vm Docker 機器

建立與 Azure docker 主機 Vm`docker-machine create`命令使用`azure`驅動程式引數為驅動程式的選項 (`-d`) 以及任何其他引數。 

下列範例會依賴預設值，但它會開啟至網際網路，才能使用 nginx 容器會讓測試的連接埠 80 VM 上`ops`登入使用者 SSH，與電話新 VM `machine`。 

輸入`docker-machine create --driver azure`若要查看的選項和其預設值。您也可以瞭解[Docker Azure 驅動程式文件](https://docs.docker.com/machine/drivers/azure/)。 （請注意，是否您有啟用雙因素驗證時，系統會提示使用的第二個因素進行驗證）。

```bash
docker-machine create -d azure \
  --azure-ssh-user ops \
  --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> \
  --azure-open-port 80 \
  machine
```

輸出看起來應該像這樣，是否有雙因素驗證，在您的帳戶設定而定。

```
Creating CA: /Users/user/.docker/machine/certs/ca.pem
Creating client certificate: /Users/user/.docker/machine/certs/cert.pem
Running pre-create checks...
(machine) Microsoft Azure: To sign in, use a web browser to open the page https://aka.ms/devicelogin. Enter the code <code> to authenticate.
(machine) Completed machine pre-create checks.
Creating machine...
(machine) Querying existing resource group.  name="machine"
(machine) Creating resource group.  name="machine" location="eastus"
(machine) Configuring availability set.  name="docker-machine"
(machine) Configuring network security group.  name="machine-firewall" location="eastus"
(machine) Querying if virtual network already exists.  name="docker-machine-vnet" location="eastus"
(machine) Configuring subnet.  name="docker-machine" vnet="docker-machine-vnet" cidr="192.168.0.0/16"
(machine) Creating public IP address.  name="machine-ip" static=false
(machine) Creating network interface.  name="machine-nic"
(machine) Creating storage account.  name="vhdsolksdjalkjlmgyg6" location="eastus"
(machine) Creating virtual machine.  name="machine" location="eastus" size="Standard_A2" username="ops" osImage="canonical:UbuntuServer:15.10:latest"
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with ubuntu(systemd)...
Installing Docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env machine
```

## <a name="configure-your-docker-shell"></a>設定您的 docker 命令介面

現在，輸入`docker-machine env <VM name>`若要查看您需要設定命令介面。 

```bash
docker-machine env machine
```

可列印的環境資訊，看起來像這樣。 請注意的 IP 位址已指派，您會需要測試 VM。

```
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://191.237.46.90:2376"
export DOCKER_CERT_PATH="/Users/rasquill/.docker/machine/machines/machine"
export DOCKER_MACHINE_NAME="machine"
# Run this command to configure your shell:
# eval $(docker-machine env machine)
```

您可以執行 [建議的設定] 命令，或您可以自行設定環境變數。 

## <a name="run-a-container"></a>執行容器

現在您可以執行簡單的網頁伺服器，以測試是否所有是否正常運作。 以下我們使用標準 nginx 圖像，指定應該聆聽連接埠 80 和如果 VM 重新啟動容器應該重新啟動以及 (`--restart=always`)。 

```bash
docker run -d -p 80:80 --restart=always nginx
```

輸出看起來應該如下所示︰

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>測試容器

檢查執行容器使用`docker ps`:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

若要查看執行容器，輸入的核取`docker-machine ip <VM name>`若要尋找的 IP 位址 (如果您忘記密碼從`env`] 命令):

![執行 ngnix 容器](./media/virtual-machines-linux-docker-machine/nginxsuccess.png)

## <a name="next-steps"></a>後續步驟

如果您有興趣，您可以嘗試執行相同作業使用 Azure CLI 或 Azure 資源管理員範本 Azure [Docker VM 副檔名](virtual-machines-linux-dockerextension.md)。 

更多範例使用 Docker 的詳細資訊，請參閱從[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 連線[示範](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)[使用 Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) 。 從 HealthClinic.biz 示範更多的快速入門，請參閱[Azure 開發人員工具快速入門](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)。

