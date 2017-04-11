<properties
    pageTitle="在 [OpenSUSE VM 上安裝 MySQL |Microsoft Azure"
    description="瞭解如何 MySQL OpenSUSE Linux VMirtual 在電腦上安裝 Azure 中。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/19/2016"
    ms.author="cynthn"/>

# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>安裝 MySQL 執行 OpenSUSE Linux 中 Azure 虛擬機器

[MySQL] [MySQL]是受歡迎、 開啟來源 SQL 資料庫。 本教學課程教您如何建立虛擬機器執行 OpenSUSE Linux，然後安裝 MySQL。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


<br>


## <a name="create-a-virtual-machine-running-opensuse-linux"></a>建立執行 OpenSUSE Linux 虛擬機器

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## <a name="install-and-run-mysql-on-the-virtual-machine"></a>安裝及執行 MySQL 虛擬機器

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## <a name="next-steps"></a>後續步驟
如需 MySQL 的詳細資訊，請參閱[MySQL 文件][MySQLDocs]。

[MySQLDocs]: http://dev.mysql.com/doc/index-topic.html
[MySQL]: http://www.mysql.com

