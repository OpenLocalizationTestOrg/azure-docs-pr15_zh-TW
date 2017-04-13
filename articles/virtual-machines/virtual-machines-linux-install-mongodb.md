<properties
   pageTitle="在 [Linux VM 上安裝 MongoDB |Microsoft Azure"
   description="瞭解如何安裝和設定 MongoDB Linux 虛擬機器 Azure 使用資源管理員部署模型中。"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>在安裝及設定 MongoDB Linux VM Azure 中
[MongoDB](http://www.mongodb.org)是常見的開啟來源、 高效能 NoSQL 資料庫。 本文將示範如何在安裝及設定 MongoDB Linux VM Azure 中使用的資源管理員部署模型。 範例會如何顯示的詳細資料至︰

- [手動安裝並設定基本的 MongoDB 執行個體](#manually-install-and-configure-mongodb-on-a-vm)
- [建立基本的 MongoDB 執行個體，使用資源管理員範本](#create-basic-mongodb-instance-on-centos-using-a-template)
- [建立複雜的 MongoDB 複本 sharded 叢集設定為使用資源管理員範本](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>必要條件
這篇文章的需求如下︰

- Azure 帳戶 ([取得免費的試用版](https://azure.microsoft.com/pricing/free-trial/))。
- [Azure CLI](../xplat-cli-install.md)登入`azure login`
- Azure CLI*必須*在 Azure 資源管理員模式使用`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>手動安裝並設定 VM MongoDB
MongoDB[提供安裝指示，](https://docs.mongodb.com/manual/administration/install-on-linux/)包括紅色角色 Linux distros / CentOS、 SUSE、 Ubuntu 及 Debian。 下列範例會建立`CoreOS`VM 使用儲存在 SSH 金鑰`.ssh/azure_id_rsa.pub`。 回答提示用於儲存體帳戶名稱與 DNS 名稱的管理員認證︰

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

使用顯示的前一個 VM 建立步驟結尾的公用 IP 位址的 vm 登入︰

```bash
ssh ops@40.78.23.145
```

若要新增 MongoDB 安裝來源，建立`yum`存放庫檔案，如下所示︰

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

開啟 MongoDB repo 檔案以進行編輯。 新增下列行︰

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

安裝 MongoDB 使用`yum`，如下所示︰

```bash
sudo yum install -y mongodb-org
```

根據預設，SELinux 會防止您存取 MongoDB CentOS 圖像上強制執行。 安裝原則管理工具和設定 SELinux 允許 MongoDB 操作其預設 TCP 連接埠 27017，如下所示。 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

啟動 MongoDB 服務，如下所示︰

```bash
sudo service mongod start
```

使用本機連線]，確認 MongoDB 安裝`mongo`用戶端︰

```bash
mongo
```

現在，新增一些資料，然後搜尋測試 MongoDB 執行個體︰

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

如有需要，設定為自動啟動期間重新啟動系統 MongoDB:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>使用範本的 CentOS 上建立基本的 MongoDB 執行個體
您可以使用下列 Azure 快速入門範本 Github 從單一 CentOS VM 上建立基本的 MongoDB 執行個體。 此範本是使用 Linux 的自訂指令碼副檔名新增`yum`您新建立的 CentOS VM 和重新安裝 MongoDB 存放庫。

- [基本 MongoDB 執行個體上 CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) -https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

下列範例會建立資源群組名稱`myResourceGroup`中`WestUS`區域。 輸入您自己的值如下所示︰

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] Azure CLI 會傳回提示建立部署，數秒內，但安裝和設定需要幾分鐘即可完成。 檢查狀態的部署`azure group deployment show myResourceGroup`，因此輸入資源群組的名稱。 等到`ProvisioningState`之前，先 vm SSH 顯示 「 成功 」。

一旦的部署完成，vm SSH。 取得您 VM 使用的 IP 位址`azure vm show`命令，如下列範例所示︰

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

輸出，結尾處`Public IP address`會顯示。 您的 IP 位址您 VM vm SSH:

```bash
ssh ops@138.91.149.74
```

使用本機連線]，確認 MongoDB 安裝`mongo`用戶端，如下所示︰

```bash
mongo
```

現在測試執行個體新增一些資料，並在搜尋，如下所示︰

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>使用範本的 CentOS 上建立複雜的 MongoDB Sharded 叢集
您可以建立複雜的 MongoDB sharded 圖組，並使用 Github 下列 Azure 快速入門範本。 此範本追蹤提供重複性和可用性[MongoDB sharded 叢集的最佳作法](https://docs.mongodb.com/manual/core/sharded-cluster-components/)。 範本會建立兩個擊碎，每個複本集合中的三個節點。 設定與三個節點的一個設定伺服器複本也會建立，再加上兩個`mongos`路由器伺服器提供擊碎從應用程式的一致性。

- [在 CentOS MongoDB Sharding 叢集](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos)-https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] 部署這個複雜的 MongoDB sharded 叢集需要 20 個以上的核心，這通常是每個訂閱的地區的預設核心計數。 開啟計數核心 Azure 支援要求。

下列範例會建立資源群組名稱`myResourceGroup`中`WestUS`區域。 輸入您自己的值如下所示︰

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] Azure CLI 會傳回提示建立部署，數秒內，但安裝和設定接手一個小時的時間才能完成。 檢查狀態的部署`azure group deployment show myResourceGroup`，根據這一點來調整您的資源群組的名稱。 等到`ProvisioningState`連線至 Vm 之前顯示 「 成功 」。


## <a name="next-steps"></a>後續步驟
在以下範例中，您連線至 MongoDB 執行個體本機從 VM。 如果您想要從另一個 VM 或網路連線至 MongoDB 執行個體，請確定[網路安全性群組規則所建立](virtual-machines-linux-nsg-quickstart.md)的適當。

如需有關如何使用範本建立的詳細資訊，請參閱[Azure 資源管理員的概觀](../azure-resource-manager/resource-group-overview.md)。

Azure 資源管理員範本下載並執行指令碼，在您的 Vm 上使用自訂指令碼副檔名。 如需詳細資訊，請參閱[使用 Azure 自訂指令碼副檔名與 Linux 虛擬機器](virtual-machines-linux-extensions-customscript.md)。