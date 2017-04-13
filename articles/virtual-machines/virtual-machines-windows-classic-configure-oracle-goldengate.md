<properties
    pageTitle="在 [Vm 中設定 Oracle GoldenGate |Microsoft Azure"
    description="逐步執行設定以及實作 Azure Windows Server Vm Oracle GoldenGate 高可用性和損毀復原的教學課程。"
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />


#<a name="configuring-oracle-goldengate-for-azure"></a>設定 Azure Oracle GoldenGate


本教學課程說明如何設定 Oracle GoldenGate 高可用性和損毀修復 Azure 虛擬機器環境。 教學課程著重在非 RAC Oracle 資料庫的[雙向複寫](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm)，並要求這兩個網站都是作用中。

Oracle GoldenGate 支援資料通訊和資料整合。 它可讓您設定資料通訊和資料同步處理方案，透過 Oracle Oracle 複寫設定，並提供非常有彈性的可用性的解決方案。 Oracle GoldenGate 補充 Oracle 資料防衛隊啟用企業資訊的分配和零停機時間升級和移轉其複寫功能。 詳細資訊，請參閱[使用 Oracle GoldenGate 與 Oracle 資料保護](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm)。

Oracle GoldenGate 包含下列主要元件︰ 擷取，資料幫浦，Replicat，軌跡或擷取檔案，檢查點、 管理員和行程。 若要有兩個網站之間的雙向複寫，必須建立並開始在這兩個網站上的所有元件。 Oracle GoldenGate 架構的詳細資訊，請參閱[Oracle GoldenGate 指南](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)。

本教學課程，假設您已經有 Oracle 資料庫可用性和損壞修復的概念，以及[Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)理論且實用的知識庫。 如需詳細資訊，請參閱[Oracle 的網站](http://www.oracle.com/technetwork/database/features/availability/index.html)。

此外，教學課程假設您已執行下列先決條件︰

- 您已經檢閱[Oracle 虛擬機器圖像-其他考量](virtual-machines-windows-classic-oracle-considerations.md)主題的可用性及損毀復原考量一節。 請注意，Azure 支援獨立 Oracle 資料庫執行個體，但不是 Oracle 實數應用程式叢集 (Oracle RAC) 目前。

- 您已從[Oracle 下載](http://www.oracle.com/us/downloads/index.html)網站下載 Oracle GoldenGate 軟體。 您已選取產品套件 Oracle 融合介軟體 – 資料整合。 然後，您已經選取 Oracle GoldenGate 上 Oracle v11.2.1 媒體套件的 Microsoft Windows x64 （64 位元） 進行 Oracle 11g 資料庫。 接下來，請下載 Oracle GoldenGate V11.2.1.0.3 11g 64 位元 Windows 2008 （64 位元）。

- Azure 在 Windows Server 上使用 Oracle 企業版中建立兩個虛擬機器 (Vm)。 請確定虛擬機器都[相同的雲端服務](virtual-machines-linux-load-balance.md)中，以確保他們可以存取彼此常設私用的 IP 位址透過相同的[虛擬網路](https://azure.microsoft.com/documentation/services/virtual-network/)中。

- 您已設定為 「 MachineGG1 」 網站 a 和 b 網站的 「 MachineGG2 」 的虛擬機器名稱在 Azure 傳統入口網站。

- 已在網站 A 和 b 網站上的 「 TestGG2 」 建立測試資料庫 」 TestGG1 」

- 您登入您的 Windows 伺服器管理員群組的成員或**ORA_DBA**群組的成員。

在本教學課程中，您將會︰

1. 設定網站 A 和 B 網站上的資料庫  

    1. 執行初始資料載入

2. 準備資料庫複寫網站 A 和 B 網站

3. 建立所有必要的物件，以支援 DDL 複寫

4. 設定網站 A 和 B 的網站上的 [GoldenGate 管理員

5. 網站 A 和 B 網站上建立擷取群組及資料幫浦處理程序

    1. 在網站的上建立解壓縮與資料幫浦程序

    2. 建立網站 B GoldenGate 檢查點表格

    3. REPLICAT 網站上新增 B

    4. 在網站 B 上建立解壓縮與資料幫浦程序

    5. 建立網站的 GoldenGate 檢查點表格

    6. REPLICAT 網站上新增 A

    7. 網站 A 和 B 網站上新增 trandata

    8. 網站的上開始擷取與資料幫浦程序

    9. 在 [網站 B 上開始擷取與資料幫浦程序

    10. 在網站的開始 REPLICAT 程序

    11. 在網站 B 上開始 REPLICAT 程序

6. 驗證雙向複寫程序

>[AZURE.IMPORTANT] 本教學課程中已設定並測試對下列軟體設定︰
>
>|                        | **網站資料庫**              | **網站 B 資料庫**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Oracle 發行**     | Oracle11g 發行 2-(11.2.0.1) | Oracle11g 發行 2-(11.2.0.1) |
>| **電腦名稱**       | MachineGG1                       | MachineGG2                       |
>| **作業系統**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle SID**         | TESTGG1                          | TESTGG2                          |
>| **複寫結構描述** | 史                            | 史                            |

為 Oracle 資料庫及 Oracle GoldenGate 後續的版本類似，可能是您需要執行一些其他變更。 最新的版本特定的資訊，請參閱[Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html)和[Oracle 資料庫](http://www.oracle.com/us/corporate/features/database-12c/index.html)的文件，請在 Oracle 的網站。 例如，發行 11.2.0.4 來源資料庫的更新版本中，DDL 擷取非同步執行 logmining 伺服器，並需要特殊的觸發程序、 表格或安裝其他資料庫物件。 可執行 oracle GoldenGate 升級，而不需要停止使用者應用程式。 使用早於版本 11.2.0.4 Oracle 11g 來源資料庫整合模式解壓縮時需要使用 DDL 觸發程序及支援物件。 如需詳細的指引，請參閱[安裝和設定 Oracle GoldenGate Oracle 資料庫](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf)。

##<a name="1-setup-database-on-site-a-and-site-b"></a>1.設定網站 A 和 B 網站上的資料庫
本節說明如何執行網站 A 和 b 網站上的資料庫的先決條件您必須在這兩個網站上執行的此區段中的所有步驟︰ 網站 A 和 b 網站。

首先，遠端桌面網站 A 和 B 網站透過 Azure 傳統入口網站。 開啟 Windows 命令提示字元，並建立主目錄 Oracle GoldenGate 安裝檔案︰

    mkdir C:\OracleGG

然後，將它解壓縮並安裝在這個資料夾中的 [Oracle GoldenGate 軟體。 這個步驟之後，您可以藉由執行下列命令來啟動 GoldenGate 軟體命令解譯 (GGSCI):

    C:\OracleGG\.\ggsci

您可以使用[GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm)執行數個設定的命令、 環境控制和監視器 Oracle GoldenGate。

接下來，請執行下列命令以建立子資料夾中所有的安裝套件︰

    GGSCI (Hostname) 1> CREATE SUBDIRS

執行下列命令以結束 GGSCI 命令提示字元︰

    GGSCI (Hostname) 1> EXIT

然後，您需要建立資料庫的使用者，會使用 Oracle GoldenGate 管理員與解壓縮複寫處理程序。 請注意，您可以建立的每一個處理程序的個別使用者，或只有一個一般使用者的設定。 在本教學課程中，我們建立一個使用者，稱為為 ggate。 然後，我們授與使用者的權限。 請注意，您必須執行下列作業的網站和網站 b。

開啟 SQL\*加號網站 A 和 B 網站上的命令視窗資料庫系統管理員權限使用**SYSDBA**，例如︰

    Enter username: / as sysdba

然後執行︰

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

接下來，找出初始化\<DatabaseSID\>。這個檔案 %oracle\_常用 %\\資料庫的網站上的資料夾和網站 B，然後將下列資料庫參數新增至 INITTEST.ora:

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

如需所有 Oracle GoldenGate GGSCI 命令的完整清單，請參閱[參考 Oracle GoldenGate windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm)。

### <a name="perform-initial-data-load"></a>執行初始資料載入

您可以依照下列幾種方法來執行資料庫中的初始資料載入。 例如，您可以使用 [ [Oracle GoldenGate 直接載入](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm)或一般匯出及匯入公用程式匯出表格資料網站此網站 b

若要示範 Oracle GoldenGate 複寫程序，本教學課程會說明網站 A 和 B 的網站上建立表格，使用下列命令。

首先，開啟 SQL\*加上命令] 視窗，然後執行下列命令以在網站 A 和 B 網站的資料庫建立庫存表格︰

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

接下來，表格加上限制式新建立的網站和網站 B 資料庫︰

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

然後，授與網站的及網站 b: 使用者 ggate 新庫存表格的所有權限

    grant all on scott.inventory to ggate;

接下來，建立並啟用資料庫上觸發程序，INVENTORY_CDR_TRG，以確保會記錄在新資料表的所有交易，如果使用者不是 ggate 新建立的資料表。 執行這項作業的網站和網站 b。

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2.準備的網站和網站 B 資料庫複寫︰
本節說明如何資料庫複寫︰ 準備網站 A 和 B 的網站。 您必須在這兩個網站上執行的此區段中的所有步驟︰ 網站 A 和 b 網站。

首先，遠端桌面網站 A 和 B 網站透過 Azure 傳統入口網站。 使用 SQL archivelog 模式切換資料庫 * 加號命令視窗︰

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


然後，啟用最小[補充記錄](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm)，如下所示︰

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

接下來，準備資料庫，以支援 DDL （資料定義語言） 複寫︰

    SQL> alter system set recyclebin=off scope=spfile;

然後、 關閉並重新啟動資料庫︰

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3.建立所有必要的物件，以支援 DDL 複寫
本節會列出您要用來建立所有必要的物件，以支援 DDL 複寫的指令碼。 您必須執行這個網站 A 和 b 網站上的一節中指定的指令碼

開啟 Windows 命令提示字元，並瀏覽至 [Oracle GoldenGate] 資料夾，例如 c:\\OracleGG。 啟動 SQL\*加號資料庫系統管理員權限，例如使用**SYSDBA**網站 A 和 b 網站上的命令提示字元

然後，執行下列指令碼︰

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Oracle GoldenGate 工具需要 DDL （資料定義語言） 支援的表格層級的登入。 這就是為什麼，啟用附加記錄的資料表層級，使用 [新增 TRANDATA] 命令。 開啟 Oracle GoldenGate 命令手語翻譯] 視窗中，登入資料庫，並執行新增 TRANDATA 命令︰

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4.在網站 A 和 B 的網站上設定 GoldenGate 管理員
Oracle GoldenGate 管理員執行函數，例如啟動其他 GoldenGate 處理程序，線索記錄檔管理與報告數的字。

您需要設定 Oracle GoldenGate 管理員程序網站 A 和 b 網站。若要這麼做，請執行下列步驟網站 A 和 b 網站。

在開啟的視窗命令] 視窗，並啟動 Oracle GoldenGate 命令解譯器︰

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


將資料庫記錄 GGSCI 工作階段，以便您可以執行影響資料庫的命令︰

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

顯示狀態，以及所有管理員、 擷取、 和 Replicat 處理程序的系統上的都延遲 （處相關）︰

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

開啟使用 [編輯參數] 命令的參數檔案，然後附加下列資訊︰

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

顯示狀態，以及所有管理員、 擷取、 和 Replicat 處理程序的系統上的都延遲 （處相關）︰

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

將資料庫記錄 GGSCI 工作階段，以便您可以執行影響資料庫的命令︰

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

啟動管理員程序︰

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5.建立擷取網站 A 和 B 網站上的群組與資料幫浦程序

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>在網站的上建立解壓縮與資料幫浦程序

您需要建立的網站和網站 A 和 B 網站透過 Azure 傳統入口網站的網站 b 遠端桌面解壓縮與資料幫浦程序。 開啟 GGSCI 命令解譯視窗。 在網站 a︰ 執行下列命令

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

開啟參數檔案使用 [編輯參數] 命令，然後附加下列資訊︰ GGSCI (MachineGG1) 18 > 編輯參數 ext1 解壓縮 ext1 使用者識別碼 ggate，密碼 ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate 表格 scott.inventory，GETBEFORECOLS （上更新 KEYINCLUDING （prod_category、 qty_in_stock last_dml），開啟刪除 KEYINCLUDING （prod_category、 qty_in_stock last_dml））。

開啟使用 [編輯參數] 命令的參數檔案，然後附加下列資訊︰

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>建立網站 B GoldenGate 檢查點表格

接下來，您需要新增檢查點表格上網站 b。若要這麼做，您需要開啟 GoldenGate 命令解譯視窗，然後執行︰

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

然後檢查點表格新增至資料庫，ggate 是擁有者的位置︰

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

新增全域檔案的目標伺服器上，也就是在此步驟中的網站 B 核取點資料表的名稱。 編輯網站 b︰ 全域檔案

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

然後，將 CHECKPOINTTABLE 參數新增至現有的全域檔案︰

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

最後一個步驟中，儲存並關閉全域參數檔案。


###<a name="add-replicat-on-site-b"></a>REPLICAT 網站上新增 B
本節說明如何新增網站 B REPLICAT 程序 」 REP2 」

使用新增 REPLICAT] 命令，在網站 b︰ 建立 Replicat 群組

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

開啟使用 [編輯參數] 命令的參數檔案，然後附加下列資訊︰

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>在網站 B 上建立解壓縮與資料幫浦程序

本節將說明如何在網站 b︰ 建立新的擷取程序 」 EXT2 」 與新資料幫浦程序 」 DPUMP2 」

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

開啟使用 [編輯參數] 命令的參數檔案，然後附加下列資訊︰

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

開啟使用 [編輯參數] 命令的參數檔案，然後附加下列資訊︰

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>建立網站的 GoldenGate 檢查點表格

Oracle GoldenGate 命令解譯視窗開啟，並建立檢查點表格︰

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

您也需要新增至網站 a 的全域檔案的核取點資料表的名稱

Oracle GoldenGate 命令手語翻譯視窗開啟和編輯網站 a︰ 全域檔案

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

儲存並關閉全域參數檔案。

###<a name="add-replicat-on-site-a"></a>REPLICAT 網站上新增 A

本節說明如何 REPLICAT 程序 」 REP1 」 網站上新增 a。

下列命令會建立 Replicat 群組 rep1 軌跡及相關聯的 checkpointtable 的名稱。

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

開啟使用 [編輯參數] 命令的參數檔案，然後附加下列資訊︰

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>網站 A 和 B 網站上新增 trandata

使用 [新增 TRANDATA] 命令，以啟用表格層級的補充記錄。 開啟 Oracle GoldenGate 命令手語翻譯] 視窗中，登入資料庫，並執行新增 TRANDATA 命令。

遠端桌面 MachineGG1，開啟 Oracle GoldenGate 命令手語翻譯，並執行︰

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

遠端桌面 MachineGG2，開啟 Oracle GoldenGate 命令手語翻譯，並執行︰

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

顯示的表格層級補充狀態資訊的記錄︰

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>網站 A 和 B 網站上新增 trandata

使用 [新增 TRANDATA] 命令，以啟用表格層級的補充記錄。 開啟 Oracle GoldenGate 命令手語翻譯] 視窗中，登入資料庫，並執行新增 TRANDATA 命令。

遠端桌面 MachineGG1，開啟 Oracle GoldenGate 命令手語翻譯，並執行︰

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

遠端桌面 MachineGG2，開啟 Oracle GoldenGate 命令解譯，並執行︰

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>網站的上開始擷取與資料幫浦程序

開始擷取程序 ext1 網站 a:

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

開始資料幫浦程序 dpump1 網站 a:

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
顯示解壓縮群組 ext1 的相關資訊︰ GGSCI (MachineGG1) 32 > 資訊擷取 ext1 擷取 EXT1 最後一開始 2013年 11 25 08:03 狀態執行檢查點延隔時間 00:00:00 (更新 00:00:02 之內) 記錄讀取檢查點 Oracle 取消復原記錄 2013年 11 25 08:03:18 Seqno 6，RBA 3230720 SCN 0.1074371 (1074371)

顯示狀態，以及所有管理員、 擷取、 和 Replicat 處理程序的系統上的都延遲 （處相關）︰

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>在 [網站 B 上開始擷取與資料幫浦程序

開始擷取程序 ext2 網站 b:

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

開始資料幫浦程序 dpump2 網站 b:

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

顯示狀態，以及所有管理員、 擷取、 和 Replicat 處理程序的系統上的都延遲 （處相關）︰

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>在網站的開始 REPLICAT 程序

本節說明如何開始 REPLICAT 程序 」 REP1 」 網站使用。

在網站 a︰ 開始 Replicat 程序

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

顯示 Replicat 群組的狀態︰

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>在網站 B 上開始 REPLICAT 程序

本節說明如何在網站 b 開始 REPLICAT 程序 」 REP2 」

在網站 b︰ 開始 Replicat 程序

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

顯示 Replicat 群組的狀態︰

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6。 驗證雙向複寫程序

若要確認 Oracle GoldenGate 設定，插入一列在網站至網站開啟 SQL A A 遠端桌面資料庫 * 加上命令視窗和執行︰ SQL > 從 v$ 資料庫; 選取名稱

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

然後檢查] 是否該資料列會在網站 b 複製若要這麼做，網站 b 開啟遠端桌面設定 SQL 加上視窗，並執行︰

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

在網站 b︰ 插入新的記錄

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

遠端桌面網站 A 和檢查複寫發生︰

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>其他資源
[Oracle Azure 虛擬機器圖像](virtual-machines-linux-classic-oracle-images.md)
