<properties
    pageTitle="具有負載平衡組 clusterize MySQL |Microsoft Azure"
    description="設定與傳統部署模型 Azure 上建立 Linux MySQL 叢集負載平衡、 高可用性"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>

# <a name="using-load-balanced-sets-to-clusterize-mysql-on-linux"></a>使用負載平衡集 clusterize linux MySQL

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


本文的目的是探索及說明才能部署 Linux 為基礎的高度可用服務，在 Microsoft Azure 探索可用性為入門 MySQL 伺服器上的不同方法。 說明這種方法的影片會提供[頻道 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)。

我們建立無共用兩個節點單一主 MySQL 可用性解決方案根據 DRBD、 Corosync 和 Pacemaker 的大綱。 只有一個節點 MySQL 執行一次。 讀取和寫入從 DRBD 資源是也限制為只有一個節點，一次。

有不是需要等 LVS VIP 方案，因為我們使用 Microsoft Azure 負載平衡集合循環功能和端點偵測、 移除和 VIP 正常復原。 VIP 是我們在第一次建立雲端服務時，由 Microsoft Azure 指派全域路由 IPv4 位址。

有 VM [VM 軍火庫](http://vmdepot.msopentech.com)上提供的 MySQL 包括 NBD 叢集、 Percona 和 Galera 以及幾個介軟體解決方案，包括至少一個其他可能架構。 只要這些解決方案與多點傳送或廣播單可以複製，而且沒有使用共用的儲存空間或多個網路介面，案例應該很容易部署 Microsoft Azure。

當然這些叢集架構可以延伸到 PostgreSQL 和 OpenLDAP 等其他產品上類似的方式。 例如，多重母片 OpenLDAP，已成功測試此負載平衡無共用程序，您可以在我們的頻道 9 部落格監看。

## <a name="getting-ready"></a>準備好

您必須以 Microsoft Azure 帳戶，可以建立至少兩個 （2) Vm 的有效訂閱 （x 已在此範例使用），網路與子網路、 相關性群組和可用性設定，以及建立新的 Vhd 雲端服務中，為相同的區域，並將其附加至 Linux Vm 的能力。

### <a name="tested-environment"></a>測試的環境

- Ubuntu 13.10
  - DRBD
  - MySQL 伺服器
  - Corosync 和 Pacemaker

### <a name="affinity-group"></a>相關性群組

解決方案相關性群組被建立登入 Azure 傳統入口網站捲動至 [設定，建立新的相關性群組。 配置更新版本所建立的資源將會指派給此相關性群組。

### <a name="networks"></a>網路

建立新的網路，及網路內建立子網路。 我們之所以選擇這個只有一個 /24 子網路內 10.10.10.0/24 網路。

### <a name="virtual-machines"></a>虛擬機器

使用 Endorsed Ubuntu 庫，建立第一個 Ubuntu 13.10 VM 而稱為`hadb01`。 此程序，稱為 hadb 中建立新的雲端服務。 我們呼叫說明我們新增更多資源時，會產生服務共用的負載平衡性質的方式。 建立`hadb01`且正常完成使用入口網站。 會自動建立 SSH 端點，並已選取 [建立的網路。 我們也選擇，建立新的可用性 Vm 設定。

我們之後 （技術上而言，建立雲端服務） 時，會建立第一個 VM 就建立第二個 VM `hadb02`。 第二個 vm 我們也會使用 Ubuntu 13.10 VM 從圖庫使用入口網站，但我們會使用現有的雲端服務，選擇`hadb.cloudapp.net`，而非建立新的項目。 我們應該自動選取網路和可用的設定。 SSH 端點將會建立太。

建立兩個 Vm 之後，我們將筆記的 SSH 連接埠`hadb01`(TCP 22) 和`hadb02`（自動指派的 Azure）

### <a name="attached-storage"></a>附加的儲存空間

我們將新的磁碟附加至這兩個 Vm，並程序中建立新的 5 GB 磁碟。 磁碟會裝載在 VHD 容器中使用我們的主要作業系統磁碟。 一旦磁碟會建立並附加是我們核心將會看到新的裝置，請重新啟動 Linux 不需要 (通常`/dev/sdc`，您可以檢查`dmesg`輸出)

我們繼續在每個 VM 上建立新的磁碟分割使用`cfdisk`（主要 Linux 磁碟分割） 及撰寫新的磁碟分割表格。 **無法建立這個資料分割上的檔案系統**。

## <a name="setting-up-the-cluster"></a>設定叢集

在兩個 Ubuntu Vm，我們需要使用 APT 安裝 Corosync、 Pacemaker 和 DRBD。 使用`apt-get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**無法安裝 MySQL 這一次**。 Debian 和 Ubuntu 安裝指令碼會在初始化 MySQL 資料目錄`/var/lib/mysql`，但由於目錄會被 DRBD 檔案系統，我們需要稍後再執行此動作。

現在我們也應該驗證 (使用`/sbin/ifconfig`) 的兩個 Vm 使用的 10.10.10.0/24 子網路中的地址與他們可以依名稱 ping 彼此。 如果有需要，您也可以使用`ssh-keygen`和`ssh-copy-id`，請確定兩個 Vm 可以透過 SSH 通訊而不需要密碼。

### <a name="setting-up-drbd"></a>設定 DRBD

我們會建立 DRBD 資源所使用的基本`/dev/sdc1`產生的磁碟分割`/dev/drbd1`資源無法使用 ext3 格式化，並用於主要和次要節點。 若要這麼做，請開啟`/etc/drbd.d/r0.res`並複製下列資源定義。 在這兩個 Vm 中執行這項操作︰

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

然後，初始化 [資源使用`drbdadm`兩個 Vm 中︰

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

最後，在主要 (`hadb01`) 強制的 DRBD 資源的擁有權 （主要）︰

    sudo drbdadm primary --force r0

如果您檢查/程序/drbd 的內容 (`sudo cat /proc/drbd`) 在這兩個 Vm，您應該會看到`Primary/Secondary`上`hadb01`和`Secondary/Primary`上`hadb02`、 解決方案此時一致。 5 GB 磁碟會同步處理到 10.10.10.0/24 網路免費為 [客戶]。

一旦磁碟已同步處理您可以建立檔案系統上`hadb01`。 如需進行測試，我們使用 ext2，但下列指示將會建立 ext3 檔案系統︰

    mkfs.ext3 /dev/drbd1

### <a name="mounting-the-drbd-resource"></a>裝載 DRBD 資源

在`hadb01`現在準備裝載 DRBD 資源。 Debian 和衍生使用`/var/lib/mysql`MySQL 的資料目錄。 由於我們尚未安裝 MySQL，我們會建立目錄，並裝載 DRBD 資源。 On `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## <a name="setting-up-mysql"></a>設定 [MySQL

現在您已準備好上安裝 MySQL `hadb01`:

    sudo apt-get install mysql-server

針對`hadb02`，有兩個選項。 您可以安裝 mysql 伺服器，這樣會建立 /var/lib/mysql 並填入新的資料目錄，然後繼續移除內容。 On `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

第二個選項可容錯移轉至`hadb02`，然後再安裝 mysql 伺服器那里 （安裝指令碼會注意到現有的安裝，並不會觸控）

On `hadb01`:

    sudo drbdadm secondary –force r0

On `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

如果您沒有立即打算容錯移轉 DRBD，第一個選項是雖然說較不典雅更容易。 此設定完成之後，您就可以開始處理您的 MySQL 資料庫。 在`hadb02`（或不論的伺服器是使用中狀態，根據 DRBD）︰

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**警告**︰ 此最後一個陳述式有效停用此表格中的根使用者驗證。 此應由您生產成績授與陳述式，並包含只適用於說明之用。

您也需要啟用網路的 MySQL 如果您想要讓查詢的外部 Vm，這是本指南的目的。 在這兩個 Vm 上開啟`/etc/mysql/my.cnf`，瀏覽至`bind-address`，從 127.0.0.1 變更 0.0.0.0。 儲存檔案之後, 議題`sudo service mysql restart`您目前的主要上。

### <a name="creating-the-mysql-load-balanced-set"></a>建立 MySQL 負載平衡設定

我們會回到入口網站，瀏覽至`hadb01`VM，然後結束點。 我們會建立新的端點，請從下拉式清單和刻度上*建立新的負載平衡設定*] 方塊中選擇 [MySQL (TCP 3306)。 我們會呼叫我們負載平衡端點`lb-mysql`。 我們會保留大部分的選項單獨除了我們將會減少，5 （秒，最小值） 的時間

建立端點後我們移至`hadb02`，結束點，然後建立新的結束點，但我們會選擇`lb-mysql`，然後從下拉式功能表中選取 [MySQL。 您也可以使用 Azure CLI 這個步驟。

這時我們具備我們需要叢集手動作業的所有項目。

### <a name="testing-the-load-balanced-set"></a>測試負載平衡設定

從外部的電腦可以執行測試，使用任何 MySQL 的用戶端，以及應用程式 (例如，做為 Azure 網站執行的 phpMyAdmin) 在這種情況下使用 MySQL 的命令列工具另一個 Linux 方塊︰

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### <a name="manually-failing-over"></a>移至手動失敗

您可以藉由關閉 MySQL、 切換 DRBD 的主要，並再次啟動 MySQL 現在模擬容錯移轉。

在 hadb01:

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

然後，在 hadb02:

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

一旦您容錯移轉手動您可以重複應該完全使用遠端查詢，而且。

## <a name="setting-up-corosync"></a>設定 Corosync

Corosync 是叢集基礎所需的 Pacemaker 搭配使用。 活動訊號 v1 和 v2 使用者 （及其他方法，例如 Ultramonkey） Corosync 時，分割的 CRM 功能，Pacemaker 仍會保留其他類似 Hearbeat 功能。

Azure Corosync 的主要限制是 Corosync 偏好代替廣播多點傳送，透過單通訊，但 Microsoft Azure 網路僅支援單。

所幸，Corosync 有工作單模式，而且只實數的限制式時，因為所有節點不都通訊彼此之間*自動*，您必須定義節點您設定檔中，包括其 IP 位址。 我們可以使用單點與只要變更繫結地址、 節點清單和記錄目錄 Corosync 範例檔案 (Ubuntu 使用`/var/log/corosync`時範例檔案使用`/var/log/cluster`) 並啟用仲裁工具。

**記事`transport: udpu`下列指示詞，以手動方式定義的 IP 位址節點**。

在`/etc/corosync/corosync.conf`兩個節點︰

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }

    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }

    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }

      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }

    quorum {
      provider: corosync_votequorum
    }

我們在兩個 Vm 複製這個設定檔，並在兩個節點開始 Corosync:

    sudo service start corosync

引進了您應該在目前建立叢集啟動服務之後，應該 constituted 仲裁。 我們可以檢視記錄檔中檢查這項功能或︰

    sudo corosync-quorumtool –l

應該先按照輸出類似下圖︰

![輸出 corosync quorumtool-l 範例](media/virtual-machines-linux-classic-mysql-cluster/image001.png)

## <a name="setting-up-pacemaker"></a>設定 Pacemaker

Pacemaker 使用叢集資源的監控、 定義主運算到下及切換這些資源至次要連結。 從一組可用的指令碼或 LSB （初始化類似） 的指令碼，其他選項之間，則可以定義資源。

我們想 Pacemaker 」 擁有 「 掛接點和 MySQL 服務 DRBD 資源。 如果 Pacemaker 可以開啟和關閉 DRBD，連接/umount 它並開始/停止 MySQL 順序正確時內容不正確的執行主要，我們設定已完成]。

當您第一次安裝 Pacemaker 時，則應該簡單，就像您的設定︰

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

核取方塊來執行`sudo crm configure show`。 現在，建立檔案 (例如`/tmp/cluster.conf`) 使用下列資源︰

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"

    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"

    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"

    primitive mysqld lsb:mysql

    group mysql fs_mysql mysqld

    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master

    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start

    property stonith-enabled=false

    property no-quorum-policy=ignore

現在載入 （您只需要一個節點中進行這項操作） 的設定︰

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

此外，請確定 Pacemaker 開始在兩個節點啟動︰

    sudo update-rc.d pacemaker defaults

幾秒鐘之後，並使用`sudo crm_mon –L`，確認您的節點的其中一個變得叢集的母片] 和 [正在執行的所有資源。 您可以使用裝載與 ps 檢查執行的資源。

下列的螢幕擷取畫面顯示`crm_mon`一個節點已停止 （結束使用控制項 C）

![停止 crm_mon 節點](media/virtual-machines-linux-classic-mysql-cluster/image002.png)

與此螢幕擷取畫面顯示兩個節點，一母片與一個從屬︰

![crm_mon 操作母片/從屬](media/virtual-machines-linux-classic-mysql-cluster/image003.png)

## <a name="testing"></a>測試

我們可以準備好進行自動容錯移轉模擬。 若要執行這兩種方式︰ 柔邊和硬碟。 柔邊的方法使用叢集的關機函數︰ ``crm_standby -U `uname -n` -v on``。 您可以使用此母片上，備用會接手。 請記住，將此值設回至 [關閉 （crm_mon 會告訴您一個節點否則已準備就緒）

硬碟方式是關閉的主要 VM (hadb01) 透過入口網站或變更 VM 即停止 (關閉） 上的層然後協助 Corosync 和 Pacemaker，我們要訊號向下的 [母片的移。 我們可以測試此 （供進行的維修作業 windows），但我們也可以強制此案例僅凍結 VM。

## <a name="stonith"></a>STONITH

很可能議題 VM 關機透過 Azure CLI 而 STONITH 指令碼控制實體裝置。 您可以使用`/usr/lib/stonith/plugins/external/ssh`做為基底和啟用 STONITH 叢集的設定。 Azure CLI 應該全域安裝，而且發佈設定/設定檔應該載入叢集的使用者。

提供[GitHub](https://github.com/bureado/aztonith)資源的範例。 您需要變更叢集的設定，藉由新增下列`sudo crm configure`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**附註︰**檢查漲跌，不會執行指令碼。 原始的 SSH 資源有 15 偵測 （ping） 檢查但 Azure VM 復原時間可能會更多的變數。

## <a name="limitations"></a>限制

下列限制︰

- 為 Pacemaker 使用中的資源管理 DRBD linbit DRBD 資源指令碼`drbdadm down`時關閉節點，即使節點，只要即將備用。 由於備用將不會同步處理 DRBD 資源母片會寫入時，這是不理想。 如果 [母片不會 graciously 失敗，備用接手較舊的檔案系統狀態。 有兩種解決此潛在的方式︰
  - 強制`drbdadm up r0`中所有的叢集節點，透過本機的 (不 clusterized) 監視，或，
  - 編輯 linbit DRBD 指令碼並確認其`down`不稱為，在`/usr/lib/ocf/resource.d/linbit/drbd`。
- 負載平衡器必須至少 5 秒若要回覆，讓應用程式應該會注意叢集，而且可以更具彈性的逾時。其他架構也可協助，例如的應用程式佇列、 查詢 middlewares 等。
- MySQL 調整是為了確保撰寫完畢 sane 的速度和快取清除至最小化記憶體遺失儘可能經常磁碟
- 撰寫效能會在 VM 相依互連虛擬切換，因為這是 DRBD 複寫裝置的機制
