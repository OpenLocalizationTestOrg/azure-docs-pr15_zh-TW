<properties
    pageTitle="更新 GitHub Azure Linux 代理程式 |Microsoft Azure"
    description="瞭解如何從 Github lateset 版本 Azure 中您 Linux VM 更新 Azure Linux 代理程式"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/14/2015"
    ms.author="mingzhan"/>


# <a name="how-to-update-the-azure-linux-agent-on-a-vm-to-the-latest-version-from-github"></a>如何從 GitHub 更新為最新版本的 VM Azure Linux 代理程式

若要更新您上 Linux VM Azure 中的[Azure Linux 代理程式](https://github.com/Azure/WALinuxAgent)，您必須已經有︰

1. 執行 Linux VM Azure 中。
2. 使用 SSH 該 Linux VM 連線。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

<br>

> [AZURE.NOTE] 如果您將會從 Windows 電腦執行這個工作，您可以使用 PuTTY SSH 到將您的 Linux 電腦。 如需詳細資訊，請參閱[如何登入虛擬機器執行 Linux](virtual-machines-linux-mac-create-ssh-keys.md)。

Azure 背書 Linux distros 必須放在其存放庫中的 Azure Linux 代理程式套件，因此請檢查並安裝最新版本從該 Distro 存放庫第一次如果可以的話。  

若是 Ubuntu，只要輸入︰

    #sudo apt-get install walinuxagent

然後，在 CentOS，輸入︰

    #sudo yum install waagent


Oracle Linux 的確認`Addons`存放庫已啟用。 選擇 [編輯檔案`/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 或`/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux)，並變更行`enabled=0`至`enabled=1`在**[ol6_addons]**或 [此檔案中的**[ol7_addons]**底下。

然後，若要安裝最新版 Azure Linux 代理程式的請輸入︰


    #sudo yum install WALinuxAgent

如果您找不到附加元件存放庫只是根據您的 Oracle Linux 版本，新增這些線條結尾的.repo 檔案︰

Oracle Linux 6 虛擬機器中︰

    [ol6_addons]
    name=Add-Ons for Oracle Linux $releasever ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
    gpgkey=http://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
    gpgcheck=1
    enabled=1

Oracle Linux 7 虛擬機器中︰

    [ol7_addons]
    name=Oracle Linux $releasever Add ons ($basearch)
    baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
    gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
    gpgcheck=1
    enabled=0

然後輸入︰

    #sudo yum update WALinuxAgent

通常這是所有您需要但如果基於某種原因，您需要安裝從 https://github.com 直接使用下列步驟。


## <a name="install-wget"></a>安裝 wget

登入使用 SSH 您 VM。

安裝的 wget （有沒有安裝依預設，例如 Redhat、 CentOS 和 Oracle Linux 版本 6.4 和 6.5 一些 distros） 輸入`#sudo yum install wget`命令列上。


## <a name="download-the-latest-version"></a>下載最新版本

在網頁上，開啟[GitHub Azure Linux 代理程式的版本](https://github.com/Azure/WALinuxAgent/releases)，找出最新的版本號碼。 (您可以找到您的目前版本輸入`#waagent --version`。)

### <a name="for-version-20x-type"></a>版本 2.0.x 中，輸入︰

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-[version]/waagent  

   下列行使用版本 2.0.14 作為範例︰

    #wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent  

### <a name="for-version-21x-or-later-type"></a>版本 2.1.x 或更新版本中，輸入︰

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-[version].zip
    #unzip WALinuxAgent-[version].zip
    #cd WALinuxAgent-[version]

   下列行使用版本 2.1.0 作為範例︰

    #wget https://github.com/Azure/WALinuxAgent/archive/WALinuxAgent-2.1.0.zip
    #unzip WALinuxAgent-2.1.0.zip  
    #cd WALinuxAgent-2.1.0

## <a name="install-the-azure-linux-agent"></a>安裝 Azure Linux 代理程式

### <a name="for-version-20x-use"></a>版本 2.0.x，使用︰

 請 waagent 可執行︰

    #chmod +x waagent

 可執行檔複製新增到 /usr/sbin /。

  針對大部分的 Linux，使用︰

    #sudo cp waagent /usr/sbin

  若是 CoreOS，請使用︰

    #sudo cp waagent /usr/share/oem/bin/

  如果這是新 Azure Linux 代理程式的安裝，請執行︰
 
    #sudo /usr/sbin/waagent -install -verbose

### <a name="for-version-21x-use"></a>版本 2.1.x，使用︰

您可能需要安裝套件`setuptools`前，請參閱[以下](https://pypi.python.org/pypi/setuptools)。 再執行︰

    #sudo python setup.py install

## <a name="restart-the-waagent-service"></a>重新啟動 waagent 服務

大部分的 linux Distros:

    #sudo service waagent restart

若是 Ubuntu，請使用︰

    #sudo service walinuxagent restart

若是 CoreOS，請使用︰

    #sudo systemctl restart waagent

## <a name="confirm-the-azure-linux-agent-version"></a>確認 Azure Linux 代理程式版本

    #waagent -version

CoreOS，如上述命令可能無法運作。

您會看到 Azure Linux 代理程式版本，已更新為新的版本。

如需有關 Azure Linux 代理程式的詳細資訊，請參閱[Azure Linux 代理程式讀我檔案](https://github.com/Azure/WALinuxAgent)。
