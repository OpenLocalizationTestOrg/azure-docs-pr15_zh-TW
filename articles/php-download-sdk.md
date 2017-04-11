<properties
    pageTitle="下載適用於 PHP Azure SDK"
    description="瞭解如何下載並安裝 Azure SDK 的 PHP。"
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>下載適用於 PHP Azure SDK

## <a name="overview"></a>概觀

Azure SDK PHP 包含元件，可讓您開發、 部署及管理 Azure PHP 應用程式。 具體來說，PHP Azure SDK 包括下列︰

* **PHP Azure 的用戶端文件庫**。 這些類別庫提供介面來存取 Azure 的功能，例如資料管理服務和雲端服務。  
* **Azure 命令列介面 for Mac、 Linux 和 Windows (Azure CLI)**。 這是一組的部署及管理 Azure 服務，例如 Azure 網站和 Azure 虛擬機器的命令。 在任何平台，包括 Mac、 Linux，以及 Windows Azure CLI 工時。
* **Azure PowerShell (僅限 Windows)**。 這是一組的部署及管理 Azure 服務，例如雲端服務與虛擬機器的 PowerShell 指令程式。
* **Azure 模擬器 (僅限 Windows)**。 計算和儲存模擬器是雲端服務，讓您測試本機應用程式的資料管理服務的本機模擬器。 Azure 模擬器只執行 Windows 上。

以下各節說明如何下載及安裝上述元件。

本主題中的指示，假設您有[PHP] [install-php]安裝。

> [AZURE.NOTE] 您必須使用 PHP 用戶端文件庫的 Azure PHP 5.5 或更新版本。

##<a name="php-client-libraries-for-azure"></a>PHP Azure 的用戶端文件庫

Azure PHP 用戶端文件庫提供介面來存取 Azure 的功能，例如資料管理服務及雲端服務，從任何作業系統。 透過編輯器，可將安裝這些文件庫。

若要瞭解如何使用 Azure PHP 用戶端文件庫，請參閱[如何使用 Blob 服務][blob-service]，[如何使用表格服務][ table-service] ，以及[如何使用佇列服務][queue-service]。

###<a name="install-via-composer"></a>安裝透過編輯器

1. [安裝給][install-git]。


    > [AZURE.NOTE] 在 Windows 上，您也必須將可執行檔給新增至您的 PATH 環境變數。

2. 建立名為**composer.json**專案的根目錄中的檔案，並為其新增下列程式碼︰

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. 下載**[composer.phar] [composer-phar]**中您專案的根目錄。

4. 開啟命令提示字元，並執行此專案根目錄中

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>Azure PowerShell 和 Azure 模擬器

Azure PowerShell 是一組的部署及管理 Azure 服務 （例如雲端服務和虛擬機器） 的 PowerShell 指令程式。 Azure 模擬器是雲端服務，讓您測試本機應用程式的資料管理服務的模擬器。 支援下列元件只 Windows。

若要安裝 PowerShell 的 Azure 和 Azure 模擬器的建議的方式是使用[Microsoft Web 平台安裝程式][download-wpi]。 請注意，您也可以選擇要安裝其他開發元件，例如 PHP、 SQL Server、 Microsoft SQL Server PHP，和 WebMatrix 驅動程式。

瞭解如何使用 PowerShell 的 Azure 資訊，請參閱[如何使用 PowerShell Azure][powershell-tools]。

##<a name="azure-cli"></a>Azure CLI

Azure CLI 是一組的部署及管理 Azure 服務，例如 Azure 網站和 Azure 虛擬機器的命令。 安裝 Azure CLI 相關資訊，請參閱[安裝 Azure CLI](xplat-cli-install.md)。

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[PHP 開發人員中心](/develop/php/)。


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
