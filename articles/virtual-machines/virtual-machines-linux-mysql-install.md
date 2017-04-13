<properties
    pageTitle="設定上 Linux VM MySQL |Microsoft Azure "
    description="瞭解如何安裝 MySQL 堆疊 Azure Linux 虛擬機器 （Ubuntu 或 RedHat 系列 OS） 上"
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
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


#<a name="how-to-install-mysql-on-azure"></a>如何安裝 MySQL Azure 上


在本文中，您將學習如何安裝和設定 MySQL 執行 Linux Azure 虛擬機器上。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


##<a name="install-mysql-on-your-virtual-machine"></a>在 [您的虛擬機器上安裝 MySQL

> [AZURE.NOTE] 您必須已經擁有 Microsoft Azure 虛擬機器執行 Linux 才能完成此教學課程。 建立及設定與 Linux VM [Azure Linux VM 教學課程](virtual-machines-linux-quick-create-cli.md)，請參閱`mysqlnode`作為 VM 名稱和`azureuser`以使用者，再繼續執行。

在此情況下，作為 MySQL 連接埠 3306 連接埠。  

連線至您建立 putty VM Linux。 如果這是您第一次使用 Azure Linux VM，請參閱如何使用 putty 連線至 Linux VM[以下](virtual-machines-linux-mac-create-ssh-keys.md)。

我們會使用存放庫套件安裝 MySQL5.6 本文中的範例。 實際上，MySQL5.6 會有更多改進比 MySQL5.5 的效能。  詳細資訊[以下](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)。


###<a name="how-to-install-mysql56-on-ubuntu"></a>如何安裝 MySQL5.6 Ubuntu 上
我們將使用 Ubuntu 從 Azure 使用 Linux VM，以下所示。

- 步驟 1︰ 安裝 MySQL 伺服器 5.6 切換至`root`使用者︰

            #[azureuser@mysqlnode:~]sudo su -

    安裝 mysql 伺服器 5.6:

            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6

    在安裝期間，您會看到] 對話方塊視窗 poping，要求您設定 MySQL 根密碼，而且您需要密碼在此設定。

    ![圖像](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p1.png)


    輸入密碼一次以確認。

    ![圖像](./media/virtual-machines-linux-mysql-install/virtual-machines-linux-install-mysql-p2.png)

- 步驟 2︰ 登入 MySQL 伺服器

    MySQL 伺服器安裝完成時，會自動啟動 MySQL 服務。 您可以登入與 MySQL 伺服器`root`使用者。
    使用下列命令以登入並輸入密碼。

             #[root@mysqlnode ~]# mysql -uroot -p

- 步驟 3︰ 管理執行 MySQL 服務

    （a） 取得 MySQL 服務的狀態

             #[root@mysqlnode ~]# service mysql status

    （b） 啟動 MySQL 服務

             #[root@mysqlnode ~]# service mysql start

    （c） 停止 MySQL 服務

             #[root@mysqlnode ~]# service mysql stop

    （d） 重新啟動 MySQL 服務

             #[root@mysqlnode ~]# service mysql restart


###<a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>如何安裝 MySQL 紅色角色 OS 系列例如 CentOS，Oracle Linux 上
我們會使用 Linux VM CentOS 或 Oracle Linux 以下。

- 步驟 1︰ 新增 MySQL Yum 存放庫切換至`root`使用者︰

            #[azureuser@mysqlnode:~]sudo su -

    下載並安裝 MySQL 發行套件︰

            #[root@mysqlnode ~]# wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm

- 步驟 2︰ 編輯下方啟用下載 MySQL5.6 套件的 MySQL 存放庫的檔案。

            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo

    下面更新此檔案的每個值︰

        \# *Enable to use MySQL 5.6*

        [mysql56-community]
        name=MySQL 5.6 Community Server

        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/

        enabled=1

        gpgcheck=1

        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

- 步驟 3︰ 安裝 MySQL 從 MySQL 存放庫安裝 MySQL:

           #[root@mysqlnode ~]#yum install mysql-community-server

    MySQL 轉速封裝並所有相關的封裝將會安裝。

- 步驟 4︰ 管理執行 MySQL 服務

    （a） 檢查服務的狀態 MySQL 伺服器︰

           #[root@mysqlnode ~]#service mysqld status

    （b） 檢查的預設連接埠的 MySQL 伺服器是否正在執行︰

           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306


    （c） 啟動 MySQL 伺服器︰

           #[root@mysqlnode ~]#service mysqld start

    （d） 停止 MySQL 伺服器︰

           #[root@mysqlnode ~]#service mysqld stop

    （e） 設定 MySQL 啟動系統啟動︰

           #[root@mysqlnode ~]#chkconfig mysqld on


###<a name="how-to-install-mysql-on-suse-linux"></a>如何安裝 MySQL SUSE linux
我們會使用 OpenSUSE 使用 Linux VM，以下所示。

- 步驟 1︰ 下載並安裝 MySQL 伺服器

    切換至`root`使用者執行以下命令︰  

           #sudo su -

    下載並安裝 MySQL 套件︰

           #[root@mysqlnode ~]# zypper update

           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql

- 步驟 2︰ 管理執行 MySQL 服務

    （a） 核取 MySQL 伺服器的狀態︰

           #[root@mysqlnode ~]# rcmysql status

    （b） 核取是否 MySQL 伺服器的預設連接埠︰

           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306


    （c） 啟動 MySQL 伺服器︰

           #[root@mysqlnode ~]# rcmysql start

    （d） 停止 MySQL 伺服器︰

           #[root@mysqlnode ~]# rcmysql stop

    （e） 設定 MySQL 啟動系統啟動︰

           #[root@mysqlnode ~]# insserv mysql

###<a name="next-step"></a>下一步
尋找更多的使用情況和 MySQL[以下](https://www.mysql.com/)的相關資訊。
