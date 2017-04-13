<properties
    pageTitle="設定上 Linux VM PostgreSQL |Microsoft Azure"
    description="瞭解如何安裝和設定 PostgreSQL Linux 中 Azure 虛擬機器"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="SuperScottz"
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="02/01/2016"
    ms.author="mingzhan"/>


# <a name="install-and-configure-postgresql-on-azure"></a>在安裝及設定 PostgreSQL Azure

PostgreSQL 是類似 Oracle 及 DB2 進階的開啟來源資料庫。 其中包含可供企業的功能，例如完整 ACID 規範與可靠的交易處理，多個版本並行控制項。 也支援標準 ANSI SQL 等 SQL/MED （包括 Oracle、 MySQL、 MongoDB，及許多其他的外部資料包裝函式）。 這是高度可延伸支援 JSON 或金鑰值為基礎的應用程式超過 12 程序的語言、 琴酒和 GiST 索引、 空間資料支援與多個 NoSQL 類似的功能。

在本文中，您將學習如何安裝和設定 PostgreSQL 執行 Linux Azure 虛擬機器上。


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="install-postgresql"></a>安裝 PostgreSQL

> [AZURE.NOTE] 您必須已經完成本教學課程，才能執行 Linux Azure 虛擬機器。 若要建立並設定 Linux VM 之前，請參閱[Azure Linux VM 教學課程](virtual-machines-linux-quick-create-cli.md)。

在此情況下，使用連接埠 1999年為 PostgreSQL 連接埠。  

連線至您建立 PuTTY VM Linux。 如果這是您使用的 Azure Linux VM 第一次，請參閱[如何使用 SSH 與 Linux Azure 上](virtual-machines-linux-mac-create-ssh-keys.md)若要瞭解如何使用 PuTTY Linux vm 連線。

1. 執行下列命令以切換至根 （系統）︰

        # sudo su -

2. 某些散佈有安裝 PostgreSQL 之前，您必須安裝的相依性。 檢查您 distro 此清單中，並執行適當的命令︰

    - 基底 Linux 紅色角色︰

            # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

    - Debian 基底 Linux:

            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  

    - SUSE Linux:

            # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  

3. 下載 PostgreSQL 的根目錄，並且然後解壓縮套件︰

        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/

        # tar jxvf  postgresql-9.3.5.tar.bz2

    上述是範例。 您可以找到詳細的下載地址[的 /pub/來源/索引](https://ftp.postgresql.org/pub/source/)中。

4. 若要開始建立，請執行下列命令︰

        # cd postgresql-9.3.5

        # ./configure --prefix=/opt/postgresql-9.3.5

5. 如果您想要建立所有項目，您可以建置，包括文件 （HTML 和男人頁面） 和其他模組 (contrib)，請改為執行下列命令︰

        # gmake install-world

    您應該會收到下列的確認訊息︰

        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>設定 PostgreSQL

1. （選用）建立符號連結來縮短 PostgreSQL 參考不包含版本號碼︰

        # ln -s /opt/pgsql9.3.5 /opt/pgsql

2. 建立資料庫目錄︰

        # mkdir -p /opt/pgsql_data

3. 建立非根使用者並修改該使用者的設定檔。 然後，切換到此新的使用者 （在本例中稱為*postgres* ）︰

        # useradd postgres

        # chown -R postgres.postgres /opt/pgsql_data

        # su - postgres

   > [AZURE.NOTE] 基於安全性理由，PostgreSQL 使用非根使用者初始化、 啟動或關閉資料庫。


4. 輸入下列命令以編輯*bash_profile*檔案。 這些線將會新增到*bash_profile*檔案結尾︰

        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF

5. 執行*bash_profile*檔︰

        $ source .bash_profile

6. 使用下列命令，以驗證您的安裝︰

        $ which psql

    如果您安裝成功，您會看到下列回應︰

        /opt/pgsql/bin/psql

7. 您也可以查看 PostgreSQL 版本︰

        $ psql -V

8. 初始化資料庫︰

        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W

    您應該會收到下列輸出︰

![圖像](./media/virtual-machines-linux-postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>設定 [PostgreSQL

<!--    [postgres@ test ~]$ exit -->

執行下列命令︰

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

修改 /etc/init.d/postgresql 檔案中的兩個變數。 前置詞設定為 [PostgreSQL 的安裝路徑︰ **/opt/pgsql**。 PGDATA 設定為 [PostgreSQL 資料儲存區路徑︰ **/opt/pgsql_data**。

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![圖像](./media/virtual-machines-linux-postgresql-install/no2.png)

變更，使其可執行檔案︰

    # chmod +x /etc/init.d/postgresql

啟動 PostgreSQL:

    # /etc/init.d/postgresql start

檢查 PostgreSQL 端點是否︰

    # netstat -tunlp|grep 1999

您應該會看到下列輸出︰

![圖像](./media/virtual-machines-linux-postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>連線至 Postgres 資料庫

同樣地，切換至 postgres 使用者︰

    # su - postgres

建立 Postgres 資料庫︰

    $ createdb events

連線到您剛剛建立的事件資料庫︰

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>建立及刪除 Postgres 表格

現在您已連線至資料庫，您可以建立表格中。

使用下列命令，例如，建立新的範例 Postgres 資料表︰

    CREATE TABLE potluck (name VARCHAR(20), food VARCHAR(30),   confirmed CHAR(1), signup_date DATE);

您現在已經設定四個資料行的資料表，下列資料行名稱與限制︰

1. 已經 VARCHAR] 命令，在 20 個字元的限制 「 名稱 」] 資料行。
2. 「 食物] 欄會指出每位人員會顯示的食物項目。 VARCHAR 限制此底下 30 個字元的文字。
3. 「 確認 」 欄記錄至聚會是否具有 RSVP'd 人員。 可接受的值是"Y"和"N"。
4. 「 日期 」 欄會顯示其註冊事件時。 Postgres 需要日期才能寫入為 dd 日 mm yyyy。

如果已成功建立表格時，您應該會看到下列動作︰

![圖像](./media/virtual-machines-linux-postgresql-install/no4.png)

您也可以使用下列命令，以檢查資料表結構︰

![圖像](./media/virtual-machines-linux-postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>將資料新增至資料表

首先，請插入一列的資訊︰

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

您應該會看到這個輸出︰

![圖像](./media/virtual-machines-linux-postgresql-install/no6.png)

您可以新增幾個更多人同時在表格。 以下是一些選項，或建立您自己︰

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>顯示表格

若要顯示表格中使用下列命令︰

    select * from potluck;

輸出為︰

![圖像](./media/virtual-machines-linux-postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>刪除表格中的資料

若要刪除表格中的資料使用下列命令︰

    delete from potluck where name=’John’;

這會刪除 「 名字 」 列中的所有資訊。 輸出為︰

![圖像](./media/virtual-machines-linux-postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>更新表格中的資料

您可以使用下列命令來更新表格中的資料。 針對此項目，黃已確認，她正在參加會議，因此我們將會變更其 RSVP 從"N"到"Y":

    UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


##<a name="get-more-information-about-postgresql"></a>取得 PostgreSQL 的相關資訊
既然您已完成的 PostgreSQL Azure Linux VM 中安裝，您可以喜歡 Azure 中使用。 若要進一步瞭解 PostgreSQL，請造訪[PostgreSQL 網站](http://www.postgresql.org/)。
