<properties
    pageTitle="使用副檔名為 CustomScript 上 Linux VM |Microsoft Azure"
    description="瞭解如何使用副檔名為 CustomScript 部署上 Linux 的虛擬機器 Azure 建立使用傳統的部署模型中的應用程式。"
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>部署 Azure CustomScript 副檔名用 Linux 燈應用程式#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Microsoft Azure CustomScript 副檔名 Linux 提供自訂您的虛擬機器 (Vm) 的方式執行任何支援 VM （例如，Python 和艦隊） 的指令碼語言撰寫的任何程式碼。 這會提供非常有彈性的方式來自動化多部電腦的應用程式部署。

您可以部署使用 Azure 傳統入口網站 Windows PowerShell 的 Azure 命令列介面 (Azure CLI) CustomScript 副檔名。

我們將使用的本文中 Azure CLI 部署 Ubuntu vm 簡單燈應用程式建立使用傳統的部署模型。

## <a name="prerequisites"></a>必要條件

此範例中，第一次建立兩個 Azure Vm 執行 Ubuntu 14.04 或更新版本。 *指令碼 vm*和*燈 vm*稱為 Vm。 當您建立 Vm 時，請使用唯一的名稱。 其中一個用來執行 CLI 命令，一個用於部署燈應用程式。

您也需要 Azure 儲存體帳戶與存取 （您可以取得這個從 Azure 傳統入口網站） 鍵。

如果您需要協助建立 Azure Linux Vm 請參閱[建立虛擬機器執行 Linux](virtual-machines-linux-classic-createportal.md)。

安裝命令假設 Ubuntu，但您可以採用的任何支援的 Linux distro 安裝。

指令碼 vm VM 必須安裝，以使用連線至 Azure Azure CLI。 相關說明請參閱[安裝及設定 Azure 命令列介面](../xplat-cli-install.md)。

## <a name="upload-a-script"></a>上傳指令碼

我們將使用副檔名為 CustomScript 遠端 VM 安裝燈堆疊並建立 PHP 頁面上執行指令碼。 我們會為 Azure blob 上載才能從任何位置存取指令碼。

### <a name="script-overview"></a>指令碼概觀

指令碼範例安裝燈堆疊 Ubuntu （包括 MySQL 無訊息安裝設定），將簡單的 PHP 檔案，並開始 Apache。

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>上傳指令碼

儲存為文字檔案，例如*install_lamp.sh*的指令碼，然後上傳至 Azure 儲存體。 您可以輕鬆地與 Azure CLI。 下列範例會將檔案上載至存放容器名為 「 指令碼]。 如果沒有容器必須先建立目錄。

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

也建立說明如何從 Azure 儲存體下載指令碼 JSON 檔案。 儲存此*public_config.json* （取代為 「 mystorage 」 儲存帳戶的名稱）︰

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>部署副檔名

現在您可以使用 [下一步] 命令來使用 Azure CLI 的遠端 vm 部署 Linux CustomScript 副檔名。

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

上一個命令下載並執行*install_lamp.sh*指令碼稱為*燈 vm*VM 上。

由於應用程式中包含網頁伺服器，請記住，使用 [下一步] 命令開啟遠端 VM 上的 HTTP 聆聽連接埠。

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>監控與疑難排解

您可以查看如何自訂指令碼會執行，位於 [遠端 VM 的記錄檔。 SSH*燈 vm*並新增了 [下一步] 命令的記錄檔。

    cd /var/log/azure/customscript
    tail -f handler.log

在執行 CustomScript 副檔名之後，您可以瀏覽至您建立的資訊的 PHP 頁面。 本文中的範例的 PHP 頁面是*http://lamp-vm.cloudapp.net/phpinfo.php*。

## <a name="additional-resources"></a>其他資源

若要部署更複雜的應用程式，您可以使用相同的基本步驟。 在此範例中的安裝指令碼儲存為公用 blob 中 Azure 儲存體。 更安全的選項就是將安裝指令碼儲存為安全 blob 的[安全存取簽章](https://msdn.microsoft.com/library/azure/ee395415.aspx)(SA)。

其他資源 Azure CLI、 Linux 及副檔名為 CustomScript 列示下一步。

[自動化 Linux VM 自訂工作使用 CustomScript 副檔名](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux 副檔名 (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux] 和 [開啟來源上 Azure 運算](virtual-machines-linux-opensource-links.md)
