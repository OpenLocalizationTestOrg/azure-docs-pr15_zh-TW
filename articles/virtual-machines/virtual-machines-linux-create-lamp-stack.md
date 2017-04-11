<properties
    pageTitle="部署 Linux 虛擬機器燈 |Microsoft Azure"
    description="瞭解如何安裝燈堆疊 Linux VM 上"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>部署 Azure 燈堆疊
本文會引導您執行部署 Apache 網頁伺服器、 MySQL，以及在 Azure PHP （燈堆疊） 的方式。 您必須 ([取得免費的試用版](https://azure.microsoft.com/pricing/free-trial/)) Azure 帳戶和[Azure CLI](../xplat-cli-install.md)的[連線至 Azure 帳戶](../xplat-cli-connect.md)。

有安裝燈本文中包含的兩種方法︰

## <a name="quick-command-summary"></a>快速命令摘要

1) 部署上新 VM 燈

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) 部署上現有的 VM 燈

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>部署上新 VM 逐步解說燈

您可以藉由建立新的[資源群組](../azure-resource-manager/resource-group-overview.md)包含 VM 開始︰

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

若要建立 VM 本身，您可以使用[GitHub 在這裡](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)找到已撰寫的 Azure 資源管理員範本。

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

您應該會看到回應提示一些其他項目︰

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

您現在已經建立 Linux VM 與燈已安裝。 如果您想要您可以跳到 [驗證燈成功安裝] 確認安裝。

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>部署上現有的 VM 逐步解說燈

如果您需要建立 Linux VM 協助您可以向 [這裡，瞭解如何建立 Linux VM] (。 / virtual-machines-linux-quick-create-cli.md)。 接下來，您會需要 SSH 到 Linux VM。 如果您需要建立 SSH 金鑰的說明您可以向 [若要瞭解如何建立 SSH 鍵 Linux/Mac 上的以下] (。 / virtual-machines-linux-mac-create-ssh-keys.md)。
如果您已經有 SSH 鍵，進入往前或 SSH 與您 Linux VM `ssh username@uniqueDNS`。

現在，您會使用您 Linux VM 中，我們會逐步 Debian 型散佈上安裝燈堆疊。 其他 Linux distros 可能會不同的確切的命令。

#### <a name="installing-on-debianubuntu"></a>Debian/Ubuntu 上安裝

您必須已安裝的下列套件︰ `apache2`， `mysql-server`， `php5`，及`php5-mysql`。 您可以安裝這些直接抓取這些套件，或使用 Tasksel。 以下是兩個選項的指示。
安裝之前，您必須下載和更新套件清單。

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>個別套件
使用引起取得︰

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>使用 Tasksel
或者，您可以下載 Tasksel，安裝多個相關的封裝協調 「 工作 」 拖曳到您的系統 Debian/Ubuntu 工具。

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

執行下列其中一項上述選項之後系統會提示您安裝這些套件及其他相依性的數字。 按下 「 y 」，然後 「 Enter 」，繼續，請依照任何其他的 MySQL 設定系統管理密碼的提示。 這將會安裝 PHP 使用 MySQL 所需的最低必要的 PHP 副檔名。 

![][1]

執行下列命令以查看其他 PHP 副檔名所提供的套件︰

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>建立 info.php 文件

您現在應該可以檢查哪個版本的 Apache 與 PHP MySQL，您可以透過命令列輸入`apache2 -v`， `mysql -v`，或`php -v`。

如果您想要進一步測試，您可以建立快速 PHP 資訊] 頁面，在瀏覽器中檢視。 使用 Nano 使用這個命令的文字編輯器建立新的檔案︰

    user@ubuntu$ sudo nano /var/www/html/info.php

GNU Nano 文字編輯器中加入下列行︰

    <?php
    phpinfo();
    ?>

然後儲存並結束文字編輯器。

重新 Apache 啟動此命令，如此所有新安裝份數才會生效。

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>驗證成功安裝的燈

現在您可以檢查 PHP 資訊您剛建立的網頁瀏覽器中移至 http://youruniqueDNS/info.php，看起來應該像這樣。

![][2]

您可以檢視 Apache2 Ubuntu 預設頁面移至您的 http://youruniqueDNS/ 檢查 Apache 安裝。 您應該會看到如下。

![][3]

恭喜您，只要設定燈堆疊上有您 Azure VM ！

## <a name="next-steps"></a>後續步驟

請查看燈堆疊的 Ubuntu 文件︰

- [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png
