<properties
    pageTitle="最佳化 Linux Vm MySQL 效能 |Microsoft Azure"
    description="瞭解如何最佳化 MySQL 是 Azure 的虛擬機器 (VM) 執行 Linux 上執行。"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="NingKuang"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="ningk"/>

#<a name="optimizing-mysql-performance-on-azure-linux-vms"></a>最佳化 Azure Linux Vm MySQL 效能

受到許多因素的影響 Azure MySQL 效能夠虛擬硬體選取項目和軟體設定。 本文著重於透過儲存空間、 系統和資料庫設定最佳化效能。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


##<a name="utilizing-raid-on-an-azure-virtual-machine"></a>利用襲擊 Azure 虛擬機器
儲存空間是會影響在雲端環境中的資料庫效能的主要因素。  相較於單一磁碟，RAID 可以提供透過並行更快速地存取。  如需更多詳細資料，請參閱[標準 RAID 層級](http://en.wikipedia.org/wiki/Standard_RAID_levels)。   

磁碟 I/O 輸出量和 Azure I/O 回應時間能夠大幅改善 RAID。 我們實驗室測試顯示磁碟 I/O，可以加倍處理量和 I/O 回應時間可以降低一半平均加倍 RAID 磁碟數目 （從 2 到 4，4 到 8 等）。 如需詳細資訊，請參閱[附錄 A](#AppendixA) 。  

除了磁碟 I/O，當您增加負荷，皆可改善 MySQL 效能。  如需詳細資訊，請參閱[附錄 B](#AppendixB) 。  

您也可以考慮區塊大小。 在 [一般如果您有較大的區塊，您會取得較低成本，特別是大型寫入。 不過，區塊大小太大時，新增其他成本，而且您無法利用 RAID。 目前的預設大小為 512 KB，這證明最常見的生產環境的最佳選擇。 [參閱如需詳細資訊。](#AppendixC)   

請注意您可以新增不同的虛擬機器類型多少磁碟上有限制。 詳細說明這些限制[虛擬機器和 Azure 雲端服務的大小](http://msdn.microsoft.com/library/azure/dn197896.aspx)。 雖然您可以選擇以設定較少的磁碟 RAID，您將需要遵循本文中的 RAID 範例 4 附加的資料磁碟。  

本文假設您已經建立 Linux 虛擬機器和 MYSQL 安裝並設定。 快速入門的詳細資訊請參閱如何安裝 MySQL Azure 上。  

###<a name="setting-up-raid-on-azure"></a>Azure 襲擊設定
下列步驟會顯示如何建立襲擊 Azure 使用 Azure 傳統入口網站。 您也可以將使用 Windows PowerShell 指令碼 RAID 設定。
在這個範例中，我們將會設定 RAID 0 與 4 的磁碟。  

####<a name="step-1-add-a-data-disk-to-your-virtual-machine"></a>步驟 1︰ 將資料磁碟新增至您的虛擬機器  

在 Azure 傳統入口網站的虛擬機器頁面上，按一下您要新增資料磁碟的虛擬機器。 在此範例中，虛擬機器是 mysqlnode1。  

![][1]

在虛擬機器中的頁面，按一下 [**儀表板**]。  

![][2]


任務列中，按一下 [**附加**]。

![][3]

然後按一下 [**附加空白磁碟**。  

![][4]

資料磁碟**主機快取喜好設定**應設定為 [**無]**。  

這會將您的虛擬機器中新增一個空白的磁碟。 重複此步驟三次，讓您有 RAID 4 資料磁碟。  

您可以查看核心訊息記錄，請參閱虛擬機器中新增的磁碟機。 例如，若要查看此 Ubuntu，請使用下列命令︰  

    sudo grep SCSI /var/log/dmesg

####<a name="step-2-create-raid-with-the-additional-disks"></a>步驟 2︰ 建立 RAID 其他磁碟
請遵循本文以瞭解詳細 RAID 設定步驟︰  

[設定軟體襲擊 Linux](virtual-machines-linux-configure-raid.md)

>[AZURE.NOTE] 如果您使用 XFS 檔案系統，請遵循下列步驟，建立 RAID 後。

若要安裝 XFS Debian、 Ubuntu，或 Linux 薄荷，請使用下列命令︰  

    apt-get -y install xfsprogs  

若要安裝 XFS 上 Fedora、 CentOS 或 RHEL，請使用下列命令︰  

    yum -y install xfsprogs  xfsdump


####<a name="step-3-set-up-a-new-storage-path"></a>步驟 3︰ 設定新的儲存空間路徑
使用下列命令︰  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

####<a name="step-4-copy-the-original-data-to-the-new-storage-path"></a>步驟 4︰ 將原始資料複製到新的儲存空間路徑
使用下列命令︰  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####<a name="step-5-modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>步驟 5︰ 修改權限，才能存取 MySQL （讀取和寫入） 資料磁碟
使用下列命令︰  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##<a name="adjust-the-disk-io-scheduling-algorithm"></a>調整磁碟 I/O 排程演算法
Linux 實作 I/O 排程演算法的四種類型︰  

-   NOOP 演算法 （無操作）
-   期限演算法 （期限）
-   完全展覽佇列演算法 (CFQ)
-   預算期間演算法 (Anticipatory)  

您可以選取不同的情況下不同的 I/O 排程，若要最佳化效能。 在完全隨機存取環境中，沒有 CFQ 與期限演算法的效能顯示較大的差異。 通常建議將 MySQL 資料庫環境設定為穩定性期限。 如果有很多循序 I/O，CFQ 可能會降低磁碟 I/O 效能。   

如 SSD 和其他設備，請使用 NOOP 或期限可以獲得更佳的效能比預設排程器。   

從 2.5 核心預設 I/O 排程演算法會是期限。 從核心 2.6.18 開始，請 CFQ 成為預設 I/O 排程演算法。  您可以指定核心啟動時，這項設定，或動態修改系統執行這項設定。  

下列範例會示範如何查看 NOOP 演算法設定預設排程器。  

Debian 分配系列中︰

###<a name="step-1view-the-current-io-scheduler"></a>步驟 1.檢視目前 I/O 排程器
使用下列命令︰  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

您會看到下列輸出，亦即指出目前排程器。  

    noop [deadline] cfq


###<a name="step-2-change-the-current-device-devsda-of-io-scheduling-algorithm"></a>步驟 2。 變更目前的裝置 (/ 開發/sda) 的排程演算法 I/O
使用下列命令︰  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

>[AZURE.NOTE] 設定此 /dev/sda 了解單獨使用不是非常有用。 它必須設定所有資料磁碟上的資料庫所在的位置。  

您應該會看到下列輸出指出該 grub.cfg 重建成功與 NOOP 的更新預設排程器。  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Redhat 分配系列，您只需要下列命令︰   

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##<a name="configure-system-file-operations-settings"></a>設定系統檔案作業
一個的最佳作法是停用在檔案系統的 atime 記錄功能。 Atime 是最後一個檔案存取時間。 隨時存取檔案時，檔案系統會記錄在記錄中的時間戳記。 不過，很少會使用這項資訊。 您可以將它停用如果您不需要可減少整體磁碟存取時間。  

若要停用 atime 記錄，您需要修改檔案系統的設定檔 /etc/ fstab 並新增 [ **noatime** ] 選項。  

例如，編輯 vim /etc/fstab 檔案，新增 noatime 如下所示。  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

然後，重新裝載檔案系統使用下列命令︰  

    mount -o remount /RAID0

測試修改過的結果。 請注意，當您修改測試檔案時，存取時間就不會更新。  

範例︰ 之前     

![][5]

範例︰ 之後

![][6]

##<a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>增加高並行系統控點的最大數目
MySQL 是高並行資料庫。 同時控點的預設數是 1024 的 Linux，其中不一定就。 **使用下列步驟，以增加支援的 MySQL 高並行系統的最大一個控點**。

###<a name="step-1-modify-the-limitsconf-file"></a>步驟 1︰ 修改 limits.conf 檔案
若要增加的最大可容許一個控點 /etc/security/limits.conf 檔案中加入下列四行。 請注意，65536 系統所支援的最大數目。   

    * 柔邊 nofile 65536
    * 硬碟 nofile 65536
    * 柔邊 nproc 65536
    * 硬碟 nproc 65536

###<a name="step-2-update-the-system-for-the-new-limits"></a>步驟 2︰ 更新為新的限制系統
執行下列命令︰  

    ulimit -SHn 65536
    ulimit -SHu 65536

###<a name="step-3-ensure-that-the-limits-are-updated-at-boot-time"></a>步驟 3︰ 確保限制會在啟動時
將下列啟動命令 /etc/rc.local 檔案中，讓它會在每個開機時才會生效。  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

##<a name="mysql-database-optimization"></a>MySQL 資料庫最佳化
若要設定為 Azure MySQL 內部部署電腦上，您可以使用相同的效能調整的策略。  

主要 I/O 最佳化規則如下︰   

-   增加快取大小。
-   減少 I/O 回應時間。  

若要最佳化 MySQL 伺服器設定，您可以更新 my.cnf 檔案，也就是伺服器和用戶端電腦的預設設定檔。  

下列設定項目會影響 MySQL 效能的主要因素︰  

-   **innodb_buffer_pool_size**︰ 緩衝集區緩衝的資料和索引的內容。 這通常是設定為 [70%的實體記憶體。
-   **innodb_log_file_size**︰ 這是取消復原記錄檔的大小。 您可以使用取消復原記錄，以確保寫入作業都快速、 可靠且可復原之後當機。 這是設定為 512 MB，可讓您足夠的空間的寫入作業的記錄。
-   **max_connections**︰ 有時應用程式不關閉連線正確。 較大的值可讓更多時間來資源回收 idled 的連線的伺服器。 連線數目上限為 10000，但建議的最大值為 5000。
-   **Innodb_file_per_table**︰ 此設定啟用或停用 InnoDB 能夠在不同的檔案儲存表格。 開啟選項可確保，可以有效率地套用數種進階的管理作業。 從效能的觀點，可加速表格空間傳輸並瓦礫管理效能最佳化。 因此，建議的設定為開啟。</br>
    從 MySQL 5.6 預設為開啟。 因此，不不需要任何動作。 其他版本，也就是早於 5.6，預設值為關閉。 需要開啟此開啟。 並應該套用之前會載入資料，因為只有新建立的資料表會影響。
-   **innodb_flush_log_at_trx_commit**︰ 預設值為 1，與範圍設定為 0 ~ 2。 預設值是獨立 MySQL 資料庫的最適合選項。 2 的設定可讓大多數的資料完整性，適合 MySQL 叢集母片。 設定為 0，可讓資料遺失，可能會影響可靠性，在某些情況下使用較佳的效能，並適合 MySQL 叢集從屬。
-   **Innodb_log_buffer_size**︰ 記錄緩衝讓交易在執行，而不必交易認可之前，清除登入到磁碟。 不過，如果有大型的二進位物件或文字欄位，快速取用快取，將會觸發經常磁碟 I/O。 更有效地放大緩衝如果 Innodb_log_waits 狀態變數不是 0。
-   **query_cache_size**︰ 最佳選擇是從開始將它停用。 將 query_cache_size 設定為 0 （現在是在 MySQL 5.6 中設定預設值），然後使用其他方法來加速查詢。  

要比較之後最佳化效能，請參閱[附錄 D](#AppendixD) 。


##<a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>開啟分析效能瓶頸 MySQL 變得很慢的查詢記錄
MySQL 變得很慢查詢記錄可協助您識別 MySQL 變得很慢的查詢。 啟用後 MySQL 變得很慢查詢記錄，您可以使用**mysqldumpslow**等的 MySQL 工具來識別效能瓶頸。  

請注意，根據預設未啟用此選項。 開啟 [變得很慢查詢記錄可能會耗用 CPU 資源。 因此，建議您啟用此暫時疑難排解效能瓶頸。

###<a name="step-1-modify-mycnf-file-by-adding-the-following-lines-to-the-end"></a>步驟 1︰ 新增下列幾行結尾修改 my.cnf 檔案   

    long_query_time = 2
    slow_query_log = 1
    slow_query_log_file = /RAID0/mysql/mysql-slow.log

###<a name="step-2-restart-mysql-server"></a>步驟 2︰ 重新啟動 mysql 伺服器
    service  mysql  restart

###<a name="step-3-check-whether-the-setting-is-taking-effect-using-the-show-command"></a>步驟 3︰ 檢查設定是否正在使用顯示] 命令的效果

![][7]   

![][8]

在此範例中，您可以看到 [變得很慢的查詢] 功能已開啟。 您可以再使用**mysqldumpslow**工具來決定效能瓶頸以及最佳化效能，例如新增索引。





##<a name="appendix"></a>附錄

以下是目標的實驗室環境時所產生的範例效能測試資料，請他們提供一般背景的效能資料趨勢不同的效能調整的解決方法，不過，結果可能會在不同環境或產品版本而有所不同。

<a name="AppendixA"></a>附錄 a:  
**使用不同的 RAID 層級的磁碟效能 (IOPS)**


![][9]

**測試命令︰**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE。注意︰ 這項測試的工作量使用 64 執行緒，嘗試連絡 RAID 的上限。

<a name="AppendixB"></a>附錄 b:  
**MySQL 效能 （處理量） 比較不同的 RAID 層級**   
（XFS 檔案系統）


![][10]  
![][11]

**測試命令︰**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**MySQL 效能 (OLTP) 比較不同的 RAID 層級**  
![][12]

**測試命令︰**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>附錄 c:   
**磁碟效能 (IOPS) 比較不同的區塊大小**  
（XFS 檔案系統）


![][13]

**測試命令︰**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

請注意此測試的檔案大小可為 30 GB 和 1 GB 分別，與 RAID 0(4 disks) XFS fie 系統。


<a name="AppendixD"></a>附錄 d:  
**MySQL 效能 （處理量） 比較之前和之後最佳化**  
（XFS 檔案系統）


![][14]

**測試命令︰**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**設定的設定預設和 optmization 如下所示︰**

|參數 |預設值    |optmization
|-----------|-----------|-----------
|**innodb_buffer_pool_size**    |無   |7 G
|**innodb_log_file_size**   |5 M |512 M
|**max_connections**    |100    |5000
|**innodb_file_per_table**  |0  |1
|**innodb_flush_log_at_trx_commit** |1  |2
|**innodb_log_buffer_size** |8 M |128 M
|**query_cache_size**   |16 M    |0


更詳細的最佳化設定參數，請參閱的 mysql 正式的指示進行。  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)  

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**測試環境**  

|硬體   |詳細資料
|-----------|-------
|Cpu    |AMD Opteron(tm) 處理器 4171 他 / 4 核心
|記憶體 |14 G
|磁碟   |10g/磁碟片
|作業系統 |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-classic-optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png
