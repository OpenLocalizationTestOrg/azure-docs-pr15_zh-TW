<properties
    pageTitle="Azure 上執行 MariaDB (MySQL) 叢集"
    description="建立 MariaDB + Galera MySQL 叢集 Azure 虛擬機器上的商務連絡人"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="sabbour"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="04/15/2015"
    ms.author="v-ahsab"/>

# <a name="mariadb-mysql-cluster---azure-tutorial"></a>MariaDB (MySQL) 叢集-Azure 教學課程

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

> [AZURE.NOTE]  MariaDB 企業叢集現在會出現在 Azure Marketplace 中。  新的服務會自動部署 MariaDB Galera 叢集手臂上。 您應該使用 https://azure.microsoft.com/en-us/marketplace/partners/mariadb/cluster-maxscale/ 從新的服務 

我們正在建立多重母片[Galera](http://galeracluster.com/products/)叢集的[MariaDBs](https://mariadb.org/en/about/)，MySQL，搭配使用高度可用的環境上 Azure 虛擬機器中的強大、 調整，且可靠掉落替代方案。

## <a name="architecture-overview"></a>架構概觀

本主題會執行下列步驟︰

1. 建立的 3 節點叢集
2. 資料磁碟分開 OS 磁碟
3. 若要增加 IOPS RAID 0/等量的設定中建立資料磁碟
4. 使用 Azure 負載平衡器平衡 3 節點的負載
5. 若要最小化重複性的工作，建立包含 MariaDB + Galera VM 圖像並使用它來建立另一個叢集 Vm。

![架構](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Setup.png)

> [AZURE.NOTE]  本主題使用[Azure CLI](../xplat-cli-install.md)工具，因此請務必下載檔案並將其連線至 Azure 訂閱根據的指示。 如果您需要在 Azure CLI 可用命令的參照，請參閱此連結進行[Azure CLI 命令參照](../virtual-machines-command-line-tools.md)。 您也需要[建立驗證的 SSH 鍵]，然後記下**.pem 檔案的位置**。


## <a name="creating-the-template"></a>建立範本

### <a name="infrastructure"></a>基礎結構

1. 建立相關性群組在一起儲存的資源

        azure account affinity-group create mariadbcluster --location "North Europe" --label "MariaDB Cluster"

2. 建立虛擬網路

        azure network vnet create --address-space 10.0.0.0 --cidr 8 --subnet-name mariadb --subnet-start-ip 10.0.0.0 --subnet-cidr 24 --affinity-group mariadbcluster mariadbvnet

3. 建立儲存帳戶裝載所有的磁碟。 請注意，您應該會放在超過 40 重度使用相同的儲存空間帳戶以避免 20000 IOPS 儲存帳戶限制磁碟。 在此案例中，我們可以遠關閉此號碼，我們會將所有項目儲存在同一個簡單的帳戶

        azure storage account create mariadbstorage --label mariadbstorage --affinity-group mariadbcluster

3. 尋找 CentOS 7 虛擬機器圖像的名稱

        azure vm image list | findstr CentOS
這會輸出類似`5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926`。 使用下列步驟中的名稱。

4. 建立使用您儲存產生的.pem SSH 索引鍵的路徑取代**/path/to/key.pem** VM 範本

        azure vm create --virtual-network-name mariadbvnet --subnet-names mariadb --blob-url "http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-os.vhd"  --vm-size Medium --ssh 22 --ssh-cert "/path/to/key.pem" --no-ssh-password mariadbtemplate 5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20140926 azureuser

5. 用於 RAID 設定 vm 附加 4 x 500 GB 資料磁碟

        FOR /L %d IN (1,1,4) DO azure vm disk attach-new mariadbhatemplate 512 http://mariadbstorage.blob.core.windows.net/vhds/mariadbhatemplate-data-%d.vhd

6. 將範本，您在**mariadbhatemplate.cloudapp.net:22**建立並使用您的私密金鑰連線 VM SSH。

### <a name="software"></a>軟體

1. 取得根

        sudo su

2. 安裝 RAID 支援︰

     - 安裝 mdadm

                yum install mdadm

     - 建立與 EXT4 檔案系統的 RAID0/條紋設定

                mdadm --create --verbose /dev/md0 --level=stripe --raid-devices=4 /dev/sdc /dev/sdd /dev/sde /dev/sdf
                mdadm --detail --scan >> /etc/mdadm.conf
                mkfs -t ext4 /dev/md0

     - 建立的連接點目錄

                mkdir /mnt/data

     - 擷取新建立的 RAID 裝置的 UUID

                blkid | grep /dev/md0

     - 編輯 /etc/fstab

                vi /etc/fstab

     - 若要啟用自動 mouting 重開機，以取得**blkid**命令之前的值取代 UUID 在該處新增裝置

                UUID=<UUID FROM PREVIOUS>   /mnt/data ext4   defaults,noatime   1 2

     - 裝載新的磁碟分割

                mount /mnt/data

3. 安裝 MariaDB:

     - 建立 MariaDB.repo 檔案︰

                vi /etc/yum.repos.d/MariaDB.repo

     - 使用下方內容

                [mariadb]
                name = MariaDB
                baseurl = http://yum.mariadb.org/10.0/centos7-amd64
                gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
                gpgcheck=1

     - 移除現有的後置和 mariadb 程式庫，以免發生衝突

            yum remove postfix mariadb-libs-*

     - 安裝 Galera MariaDB

            yum install MariaDB-Galera-server MariaDB-client galera

4. 移至 RAID 區塊裝置的 [MySQL 資料目錄

     - 將目前的 MySQL 目錄，複製到新的位置，並移除舊的目錄

            cp -avr /var/lib/mysql /mnt/data  
            rm -rf /var/lib/mysql

     - 依照在新的目錄上設定權限

            chown -R mysql:mysql /mnt/data && chmod -R 755 /mnt/data/  

     - 建立指向 RAID 磁碟分割的新位置的舊目錄 symlink

            ln -s /mnt/data/mysql /var/lib/mysql

5. 因為[SELinux 會干擾叢集操作](http://galeracluster.com/documentation-webpages/configuration.html#selinux)，則必須將它停用目前的工作階段 （相容的版本，直到出現為止）。 編輯`/etc/selinux/config`停用它後續的重新啟動︰

            setenforce 0

       then editing `/etc/selinux/config` to set `SELINUX=permissive`

6. 驗證 MySQL 執行

    - 開始 MySQL

            service mysql start

    - 安全的 MySQL 安裝、 設定根密碼、 移除匿名使用者，停用遠端根登入和移除測試資料庫

            mysql_secure_installation

    - 建立使用者在資料庫中的叢集操作和 （選擇性），您的應用程式

            mysql -u root -p
            GRANT ALL PRIVILEGES ON *.* TO 'cluster'@'%' IDENTIFIED BY 'p@ssw0rd' WITH GRANT OPTION; FLUSH PRIVILEGES;
            exit

   - 停止 MySQL

            service mysql stop

7. 建立設定版面配置區

    - 編輯 MySQL 的設定來建立叢集設定版面配置區。 不會取代**`<Vairables>`**或立即取消註解。 將會從這個範本，我們建立 VM 之後。

            vi /etc/my.cnf.d/server.cnf

    - 編輯 [ **[galera]** ] 區段，並清除其

    - 編輯**[mariadb]**區段

            wsrep_provider=/usr/lib64/galera/libgalera_smm.so
            binlog_format=ROW
            wsrep_sst_method=rsync
            bind-address=0.0.0.0 # When set to 0.0.0.0, the server listens to remote connections
            default_storage_engine=InnoDB
            innodb_autoinc_lock_mode=2

            wsrep_sst_auth=cluster:p@ssw0rd # CHANGE: Username and password you created for the SST cluster MySQL user
            #wsrep_cluster_name='mariadbcluster' # CHANGE: Uncomment and set your desired cluster name
            #wsrep_cluster_address="gcomm://mariadb1,mariadb2,mariadb3" # CHANGE: Uncomment and Add all your servers
            #wsrep_node_address='<ServerIP>' # CHANGE: Uncomment and set IP address of this server
            #wsrep_node_name='<NodeName>' # CHANGE: Uncomment and set the node name of this server

8. （CentOS 7 上使用 FirewallD） 的防火牆上開啟必要的連接埠

    - MySQL:`firewall-cmd --zone=public --add-port=3306/tcp --permanent`
    - GALERA:`firewall-cmd --zone=public --add-port=4567/tcp --permanent`
    - GALERA IST:`firewall-cmd --zone=public --add-port=4568/tcp --permanent`
    - RSYNC:`firewall-cmd --zone=public --add-port=4444/tcp --permanent`
    - 重新載入防火牆︰`firewall-cmd --reload`

9.  最佳化效能的系統。 請參考本文[效能調整策略](virtual-machines-linux-classic-optimize-mysql.md)如需詳細資訊

    - 再次編輯 MySQL 設定檔

            vi /etc/my.cnf.d/server.cnf

    - 編輯**[mariadb]**區段，然後附加下方

    > [AZURE.NOTE] 建議**innodb\_緩衝\_pool_size**是 [70%的您 VM 的記憶體。 設定為以下 2.45 GB 中型 Azure vm 3.5 gb 的 RAM。

            innodb_buffer_pool_size = 2508M # The buffer pool contains buffered data and the index. This is usually set to 70% of physical memory.
            innodb_log_file_size = 512M #  Redo logs ensure that write operations are fast, reliable, and recoverable after a crash
            max_connections = 5000 # A larger value will give the server more time to recycle idled connections
            innodb_file_per_table = 1 # Speed up the table space transmission and optimize the debris management performance
            innodb_log_buffer_size = 128M # The log buffer allows transactions to run without having to flush the log to disk before the transactions commit
            innodb_flush_log_at_trx_commit = 2 # The setting of 2 enables the most data integrity and is suitable for Master in MySQL cluster
            query_cache_size = 0

10. 停止 MySQL、 停用在啟動時，若要避免當新增新的節點，不必叢集執行 MySQL 服務及 deprovision 電腦。

        service mysql stop
        chkconfig mysql off
        waagent -deprovision

11. 擷取 VM 透過入口網站。 （目前[問題 Azure CLI 中的 # 1268年]工具說明 Azure CLI 工具所擷取的影像無法擷取附加的資料磁碟 fact）。

    - 關閉入口網站透過機器
    - 按一下 [擷取和指定圖像名稱為**mariadb galera 映像**提供描述，並核取 [我有執行 waagent]。
    ![擷取虛擬機器](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture.png)
    ![擷取虛擬機器](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Capture2.PNG)

## <a name="creating-the-cluster"></a>建立叢集

建立 3 Vm 不在您剛建立，然後設定和啟動叢集的範本。

1. 建立第一個 CentOS 7 VM，從您建立的**mariadb galera 映像**圖像、 提供虛擬網路名稱**mariadbvnet**和子網路**mariadb**電腦大小**中型**傳遞雲端服務中名稱為**mariadbha** （或您想要透過 mariadbha.cloudapp.net 任意命名），此名稱電腦**mariadb1**和要**azureuser**的使用者名稱與啟用 SSH 存取與傳遞 SSH 憑證.pem 檔案及取代的路徑**/path/to/key.pem**位置您儲存產生的.pem SSH 鍵。

    > [AZURE.NOTE] 下列命令會分割為多個線條，為避免混淆，但您必須輸入每一行為。

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 22
        --vm-name mariadb1
        mariadbha mariadb-galera-image azureuser

2. 建立 2 的虛擬機器_連線_至目前建立**mariadbha**雲端服務的唯一的連接埠與相同的雲端服務中的其他 Vm 不衝突變更**VM 名稱**，以及**SSH 連接埠**。

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 23
        --vm-name mariadb2
        --connect mariadbha mariadb-galera-image azureuser
以及 MariaDB3

        azure vm create
        --virtual-network-name mariadbvnet
        --subnet-names mariadb
        --availability-set clusteravset
        --vm-size Medium
        --ssh-cert "/path/to/key.pem"
        --no-ssh-password
        --ssh 24
        --vm-name mariadb3
        --connect mariadbha mariadb-galera-image azureuser

3. 您將需要每個 3 Vm 內部 IP 位址取得下一個步驟︰

    ![取得 IP 位址](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/IP.png)

4. 將 3 Vm SSH 和編輯每個的設定檔

        sudo vi /etc/my.cnf.d/server.cnf

    uncommenting**`wsrep_cluster_name`**和**`wsrep_cluster_address`**移除**#**的開頭和驗證它們是確實您想要。
    此外，取代**`<ServerIP>`**中**`wsrep_node_address`**和**`<NodeName>`**中**`wsrep_node_name`**VM 的 ip 位址和分別名稱以及這些行取消註解。

5. 開始 MariaDB1 叢集，讓它在啟動時執行

        sudo service mysql bootstrap
        chkconfig mysql on

6. 啟動 MySQL MariaDB2 和 MariaDB3，並讓其在啟動時執行

        sudo service mysql start
        chkconfig mysql on

## <a name="load-balancing-the-cluster"></a>負載平衡叢集
當您建立直的 Vm 時，加入到可用性設定稱為**clusteravset**確保他們會在不同的錯誤和更新的網域及的 Azure 永遠不會在所有電腦上進行的維修作業一次。 此設定符合需求，以支援，該 Azure 服務等級協定 (SLA)。

現在您可以使用 Azure 負載平衡器平衡我們 3 節點之間的要求。

執行下列使用 Azure CLI 您電腦上的命令。
命令參數結構是︰`azure vm endpoint create-multiple <MachineName> <PublicPort>:<VMPort>:<Protocol>:<EnableDirectServerReturn>:<Load Balanced Set Name>:<ProbeProtocol>:<ProbePort>`

    azure vm endpoint create-multiple mariadb1 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb2 3306:3306:tcp:false:MySQL:tcp:3306
    azure vm endpoint create-multiple mariadb3 3306:3306:tcp:false:MySQL:tcp:3306

最後，因為 CLI 負載平衡器探查將間隔設定為 15 秒 （其中可能有點太長），變更在入口網站中**的端點**下 Vm 的任何

![編輯端點](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint.PNG)

然後按一下 Reconfigure The Load-Balanced 設定，然後進行下一個步驟

![重新設定負載平衡的設定](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint2.PNG)

然後探查間隔變更為 5 秒並儲存

![變更探查間隔](./media/virtual-machines-linux-classic-mariadb-mysql-cluster/Endpoint3.PNG)

## <a name="validating-the-cluster"></a>驗證叢集

完成工作。 叢集能立即存取在`mariadbha.cloudapp.net:3306`這會按負載平衡器，並傳送邀請 3 Vm 之間順暢; 並有效率地。

使用您最愛的 MySQL 用戶端連線，或只是從驗證此叢集正在 Vm 的其中一個連線。

     mysql -u cluster -h mariadbha.cloudapp.net -p

然後建立新的資料庫，並填入一些資料

    CREATE DATABASE TestDB;
    USE TestDB;
    CREATE TABLE TestTable (id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, value VARCHAR(255));
    INSERT INTO TestTable (value)  VALUES ('Value1');
    INSERT INTO TestTable (value)  VALUES ('Value2');
    SELECT * FROM TestTable;

下表會導致

    +----+--------+
  	| id | value  |
    +----+--------+
  	|  1 | Value1 |
  	|  4 | Value2 |
    +----+--------+
    2 rows in set (0.00 sec)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>後續步驟

在本文中，您建立了 3 節點 MariaDB + Galera 高度可用叢集上 Azure 虛擬機器執行 CentOS 7。 Vm 是負載平衡與 Azure 負載平衡器。

若要查看[叢集 linux MySQL 另一種方法](virtual-machines-linux-classic-mysql-cluster.md)和[最佳化](virtual-machines-linux-classic-optimize-mysql.md)並測試 Azure Linux Vm MySQL 效能的方式。

<!--Anchors-->
[Architecture overview]: #architecture-overview
[Creating the template]: #creating-the-template
[Creating the cluster]: #creating-the-cluster
[Load balancing the cluster]: #load-balancing-the-cluster
[Validating the cluster]: #validating-the-cluster
[Next steps]: #next-steps

<!--Image references-->

<!--Link references-->
[Galera]: http://galeracluster.com/products/
[MariaDBs]: https://mariadb.org/en/about/
[建立驗證的 SSH 鍵]:http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/
[Azure CLI # 1268年問題]:https://github.com/Azure/azure-xplat-cli/issues/1268
