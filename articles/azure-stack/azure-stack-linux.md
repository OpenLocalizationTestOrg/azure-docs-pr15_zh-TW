<properties
    pageTitle="Azure 堆疊 Linux 來賓 |Microsoft Azure"
    description="瞭解如何建立 Azure 堆疊上的 [Linux 型虛擬機器。"
    services="azure-stack"
    documentationCenter=""
    authors="anjayajodha"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anajod"/>
    
# <a name="deploy-linux-virtual-machines-on-azure-stack"></a>部署 Azure 堆疊上 Linux 虛擬機器

您可以將 Linux 為基礎的圖像新增至 Azure 堆疊 Marketplace 部署 Linux 虛擬機器上 Azure 堆疊 POC。 多個 Linux 廠商有提供可新增至 Azure 堆疊 POC 的圖像或建立您自己。

## <a name="download-an-image"></a>下載圖像

 1. 下載並從下列連結，擷取相容於 Azure 堆疊的圖像或準備自己︰
  - [Bitnami](https://bitnami.com/azure-stack)
  - [CentOS](http://olstacks.cloudapp.net/latest/)
  - [CoreOS](https://stable.release.core-os.net/amd64-usr/current/coreos_production_azure_image.vhd.bz2)
  - [SuSE](https://download.suse.com/Download?buildid=VCFi7y7MsFQ~)
  - [Ubuntu 14.04 LTS](https://partner-images.canonical.com/azure/azure_stack/) / [Ubuntu 16.04 LTS](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
  
 2. 擷取的圖像 VHD 必要及[新增至服務商場圖像](azure-stack-add-vm-image.md)。 請確定`OSType`參數設定為`Linux`。
 
 3. 您已新增圖像至服務商場之後，請建立服務商場項目，然後您可以佈署 Linux 虛擬機器。
  
## <a name="prepare-your-own-image"></a>準備您自己的圖像

1. 準備使用其中一個下列指示自己 Linux 圖像︰
 - [CentOS 型分配](../virtual-machines/virtual-machines-linux-create-upload-centos.md)
 - [Debian Linux](../virtual-machines/virtual-machines-linux-debian-create-upload-vhd.md)
 - [Oracle Linux](../virtual-machines/virtual-machines-linux-oracle-create-upload-vhd.md)
 - [紅色的角色企業 Linux](../virtual-machines/virtual-machines-linux-redhat-create-upload-vhd.md)
 - [SLES 與 openSUSE](../virtual-machines/virtual-machines-linux-suse-create-upload-vhd.md)
 - [Ubuntu](../virtual-machines/virtual-machines-linux-create-upload-ubuntu.md)

2. 下載並安裝[Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent/)

    Azure Linux 代理程式版本 2.1.3 或更高的必要佈建 Azure 堆疊上您 Linux VM。 已上述散佈許多其存放庫中包含此版本或更高為套件的代理程式 (通常稱為`WALinuxAgent`或`walinuxagent`)。 不過，Azure 代理程式套件的版本是否小於 2.1.3 （亦即 2.0.18 或較低），然後您就必須手動安裝代理程式。 可以決定所安裝的版本，從 [套件名稱，或執行`/usr/sbin/waagent -version`VM 上。

    依照指示操作來手動-安裝 Azure Linux 代理程式

 - 首先，請從[Github](https://github.com/Azure/WALinuxAgent/releases)，下載最新的 Azure Linux 代理程式範例︰

            # wget https://github.com/Azure/WALinuxAgent/archive/v2.2.0.tar.gz

 - 解壓縮 Azure 代理程式︰

            # tar -vzxf v2.2.0.tar.gz

 - 安裝 python setuptools

        **Debian / Ubuntu**

            # sudo apt-get update
            # sudo apt-get install python-setuptools

        **Ubuntu 16.04+**

            # sudo apt-get install python3-setuptools

        **RHEL / CentOS / Oracle Linux**

            # sudo yum install python-setuptools

 - 安裝 Azure 代理程式︰

            # cd WALinuxAgent-2.2.0
            # sudo python setup.py install --register-service

    系統與 Python 2.x 和 Python 3.x 安裝-並存可能需要執行下列命令︰

        # sudo python3 setup.py install --register-service

    如需詳細資訊，請參閱 Azure Linux 代理程式[讀我檔案](https://github.com/Azure/WALinuxAgent/blob/master/README.md)。

3. [新增服務商場的圖像](azure-stack-add-vm-image.md)。 請確定`OSType`參數設定為`Linux`。

4. 您已新增圖像至服務商場之後，請建立服務商場項目，然後您可以佈署 Linux 虛擬機器。

## <a name="next-steps"></a>後續步驟

[Azure 堆疊的常見問題](azure-stack-faq.md)