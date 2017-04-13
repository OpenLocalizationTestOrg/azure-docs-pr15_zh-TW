<properties
    pageTitle="在 Windows VM 上安裝 MongoDB |Microsoft Azure"
    description="瞭解如何建立與執行 Windows Server 傳統部署模型 Azure VM 上安裝 MongoDB。"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>在 Windows VM 上安裝 MongoDB

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]若要安裝並設定 MongoDB 使用資源管理員部署模型，請參閱[本文](virtual-machines-windows-classic-install-mongodb.md)。

[MongoDB] [MongoDB]是常見的開啟來源、 高效能 NoSQL 資料庫。 本文會引導您完成建立 Windows Server 的虛擬機器 (VM) 使用[Azure 傳統入口網站][AzurePortal]。 然後，您會建立並將資料磁碟附加至之前安裝及設定 MongoDB VM。 如果您有現有的 VM 您想要使用的 Azure 中時，您可以直接跳到[安裝及設定 MongoDB](#install-and-run-mongodb-on-the-virtual-machine)。


## <a name="create-a-virtual-machine-running-windows-server"></a>建立執行 Windows Server 的虛擬機器

請依照這些指示建立虛擬機器。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] 您可以的 MongoDB 新增端點，建立虛擬機器，時，並將它設定，如下所示︰ 將其命名為**Mongo**、 **TCP**作為通訊協定，以及設定公開及私密金鑰連接埠至**27017**。

## <a name="attach-a-data-disk"></a>附加資料磁碟
若要提供虛擬機器中的儲存空間，附加資料磁碟，然後將它初始化讓 Windows 才能使用它。 如果您已經有資料磁碟，您可以將附加的現有的磁碟，或您可以附加空的磁碟。

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

初始化磁碟上的指示，請參閱 「 如何︰ 初始化 Windows Server 的新資料磁碟 」 如何[附加到 Windows 虛擬機器資料磁碟](virtual-machines-windows-classic-attach-disk.md)。

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>安裝及執行 MongoDB 虛擬機器

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>摘要
在本教學課程中，您學到如何建立執行 Windows Server 的虛擬機器、 遠端連線，以及附加資料磁碟。  您也可以瞭解如何安裝和設定 MongoDB Windows 型虛擬機器上。 您現在可以存取使用 Windows 型虛擬機器 MongoDB，按照[MongoDB 文件]中的進階的主題[MongoDocs]。

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com
