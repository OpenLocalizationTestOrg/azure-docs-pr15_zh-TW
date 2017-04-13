<properties
    pageTitle="設定 Oracle 資料保護中 Vm |Microsoft Azure"
    description="逐步執行設定，以及在高可用性和損毀復原 Azure 虛擬機器上實作 Oracle 資料保護的教學課程。"
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

#<a name="configuring-oracle-data-guard-for-azure"></a>設定 Azure Oracle 資料的保護


本教學課程說明如何設定和高可用性和損毀修復 Azure 虛擬機器環境中實作 Oracle 資料保護。 教學課程的重點在於為非 RAC Oracle 資料庫複寫。

Oracle 資料保護支援 Oracle 資料庫資料保護與損毀修復。 很簡單高效能、 掉落解決方案損毀修復與資料保護整個 Oracle 資料庫的可用性。

本教學課程，假設您已經有 Oracle 資料庫可用性和損壞修復概念理論且實用的知識庫。 如需的資訊，請參閱[Oracle 網站](http://www.oracle.com/technetwork/database/features/availability/index.html)和也[Oracle 資料保護的概念與管理輔助線](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm)。

此外，教學課程假設您已執行下列先決條件︰

- 您已經檢閱[Oracle 虛擬機器圖像-其他考量](virtual-machines-windows-classic-oracle-considerations.md)主題的可用性及損毀復原考量一節。 Azure 目前支援獨立 Oracle 資料庫執行個體，但不是 Oracle 實數應用程式叢集 (Oracle RAC)。


- 您已使用相同的平台提供 Oracle 企業版圖像的 Azure 中建立兩個虛擬機器 (Vm)。 請確定虛擬機器有[相同的雲端服務](virtual-machines-windows-load-balance.md)中相同的虛擬網路，以確保他們可以存取彼此透過常設私用的 IP 位址。 此外，建議您將 Vm 放在同一個[顯示狀態設定](virtual-machines-windows-manage-availability.md)為允許 Azure 放置到另一個錯誤網域及升級網域。 Oracle 資料保護僅適用於使用 Oracle 資料庫企業版的。 每一部電腦必須至少 2 GB 的記憶體和 5 GB 的磁碟空間。 提供 VM 大小的平台上的最新資訊，請參閱[Azure 虛擬機器大小](virtual-machines-windows-sizes.md)。 如果您需要額外的磁碟區為您的 Vm 時，您可以附加其他磁碟。 資訊，請參閱[如何將附加至虛擬機器資料磁碟](virtual-machines-windows-classic-attach-disk.md)。



- 您已設定虛擬機器名稱為 「 Machine1 」 的主要 VM 和 「 Machine2 」 備用 vm 在 Azure 傳統入口網站。

- 您已設定**ORACLE_HOME**環境變數，例如指向主要及待命虛擬機器中相同的 oracle 根安裝路徑`C:\OracleDatabase\product\11.2.0\dbhome_1\database`。

- 您登入您的 Windows 伺服器**管理員**群組的成員或**ORA_DBA**群組的成員。

在本教學課程中，您將會︰

實作實體備用資料庫環境

1. 建立主要資料庫

2. 準備備用資料庫建立主要資料庫

    1. 啟用強制的記錄

    2. 建立密碼檔案

    3. 設定備用取消復原記錄檔

    4. 啟用封存

    5. 設定主要資料庫初始化參數

建立實體備用資料庫

1. 準備備用資料庫的初始參數檔案

2. 設定接聽和 tnsnames 支援主要和備用電腦上的資料庫

    1. 如要保留項目的這兩個資料庫伺服器設定 listener.ora

    2. 若要保留項目的主要和備用資料庫、 設定 tnsnames.ora 主要和備用虛擬機器上。 

    3. 啟動接聽程式，並檢查兩個服務的兩個虛擬機器上 tnsping。

3. 啟動 nomount 狀態的備用執行個體

4. 若要複製的資料庫，並建立備用資料庫使用 RMAN

5. 在受管理的復原模式中啟動實際的備用資料庫

6. 驗證實體備用資料庫

> [AZURE.IMPORTANT] 本教學課程中具有已設定並測試對下列硬體與軟體設定︰
>
>|                      | **主要資料庫**                      | **備用資料庫**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Oracle 發行**   | Oracle11g 企業版 (11.2.0.4.0) | Oracle11g 企業版 (11.2.0.4.0) |
>| **電腦名稱**     | Machine1                                  | Machine2                                  |
>| **作業系統** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle SID**       | 測試                                      | 測試\_STBY                                |
>| **記憶體**           | 最小值 2 GB                                  | 最小值 2 GB                                  |
>| **磁碟空間**       | 最小值 5 GB                                  | 最小值 5 GB                                  |

後續 Oracle 資料庫及 Oracle 資料保留的版本，可能是您需要執行一些其他變更。 最新版本特定的資訊，請參閱在 Oracle 網站的[資料保護](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html)與[Oracle 資料庫](http://www.oracle.com/us/corporate/features/database-12c/index.html)文件。

##<a name="implement-the-physical-standby-database-environment"></a>實作實體備用資料庫環境
### <a name="1-create-a-primary-database"></a>1.建立主要資料庫

- 建立主要資料庫 」 測試 「 主要的虛擬機器中。 如需的資訊，請參閱建立及設定 Oracle 資料庫。
- 若要查看您的資料庫名稱，連線到您的資料庫作為 SQL SYSDBA 角色系統使用者 * 加號命令提示字元中，執行下列陳述式︰

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- 接下來，查詢從 dba_data_files 的 [系統] 檢視的資料庫檔案的名稱︰

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2.準備備用資料庫建立主要資料庫

建立之前備用資料庫，建議您確定已正確設定的主要資料庫。 以下是您需要執行的步驟的清單︰

1. 啟用強制的記錄
2. 建立密碼檔案
3. 設定備用取消復原記錄檔
4. 啟用封存
5. 設定主要資料庫初始化參數

#### <a name="enable-forced-logging"></a>啟用強制的記錄

若要實作待命資料庫，我們需要啟用主要資料庫中的 [強制記錄 」。 這個選項可確保即使 'nologging' 作業完成後，強制記錄的優先順序，並且所有作業都登入取消復原記錄。 因此，我們確認主要資料庫中的所有項目會記錄，並待命複寫主要資料庫中包含所有作業。 若要啟用強制記錄，請執行 alter database 陳述式︰

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>建立密碼檔案

若要能夠將運送並套用到待命伺服器主要伺服器上封存的記錄，系統密碼必須相同主要和備用伺服器上。 這就是為什麼您建立的主要資料庫密碼檔案，並將其複製到待命伺服器。

>[AZURE.IMPORTANT] 使用 Oracle 資料庫 12 c，當有新的使用者， **SYSDG**，您可以用來管理 Oracle 資料保護。 如需詳細資訊，請參閱[Oracle 資料庫 12 c 版本中的變更](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404)。

此外，請確認 ORACLE\_常用環境中已有了 Machine1。 如果不是，請將其定義為環境變數使用環境變數] 對話方塊。 若要存取此對話方塊，先**系統**公用程式按兩下 [系統] 圖示，在**[控制台]**。然後按一下 [**進階**] 索引標籤，然後選擇 [**環境變數**。 若要設定環境變數，請按一下 [**新增**] 按鈕底下**系統變數**。 設定後的環境變數，關閉現有的 Windows 命令提示字元，並開啟新的設定。

執行下列陳述式，切換至 Oracle\_常用的目錄，例如 c:\\OracleDatabase\\產品\\11.2.0\\dbhome\_1\\資料庫。

    cd %ORACLE_HOME%\database

然後，建立密碼檔案建立公用程式， [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm)密碼檔案。 在同一個 Windows 命令提示字元中 Machine1，執行下列命令密碼值設定為**系統**的密碼︰

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

這個命令會建立密碼檔案]，將您指派為 PWDTEST.ora，oracle\_常用\\資料庫目錄。 您應該將檔案複製到 %oracle\_常用 %\\手動資料庫中 Machine2 目錄。

#### <a name="configure-a-standby-redo-log"></a>設定備用取消復原記錄檔

然後，您需要設定備用取消復原記錄，使其變成備用時，主要正確會收到取消復原。 預先以下建立時，也可讓自動建立準備就緒備用取消復原記錄。 請務必為線上的取消復原記錄設定為相同大小待命取消復原記錄 (SRL)。 目前的備用取消復原記錄檔的大小必須確實符合目前的主要資料庫線上取消復原記錄檔的大小。

在 SQL 中執行下列陳述式\*加號 Machine1 中的命令提示字元。 V$ 記錄檔是 [系統] 檢視包含取消復原記錄檔的相關資訊。

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


請注意，52428800 50 mb。

然後，在 SQL\*加號] 視窗中，執行下列陳述式，將新的備用取消復原記錄檔群組加入至備用資料庫，並指定識別使用群組子句的群組的數字。 使用群組的數字，可以進行管理備用取消復原您更容易的記錄檔群組︰

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

接下來，執行下列動作的 [系統] 檢視清單資訊取消復原記錄檔。 這項作業也會驗證所建立備用取消復原記錄檔群組︰

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>啟用封存

然後，啟用藉由執行下列陳述式來保留 ARCHIVELOG 模式中的主要資料庫，並啟用自動封存封存。 您可以啟用封存記錄模式裝載資料庫，並執行 archivelog] 命令。

首先，sysdba 身分登入。 在 Windows 命令提示字元中，執行︰

    sqlplus /nolog

    connect / as sysdba

然後關閉資料庫的 SQL\*加號命令提示字元︰

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

然後，執行啟動裝載命令裝載資料庫。 如此一來，可確保 Oracle 產生關聯的執行個體，以指定資料庫。

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

然後，執行︰

    SQL> alter database archivelog;
    Database altered.

然後執行 Alter database 陳述式以讓資料庫可供使用標準開啟子句︰

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>設定主要資料庫初始化參數

若要設定資料保護，您需要建立及設定備用參數一般 pfile （文字初始化參數檔案） 的第一個。 當 pfile 時，必須先將其轉換為伺服器參數檔案 (SPFILE)。

您可以控制初始化中使用參數的資料保護環境。這個檔案。 遵循此教學課程中，當您需要更新初始化的主要資料庫。這個，讓它可以按住這兩種角色︰ 主要或待命。

    SQL> create pfile from spfile;
    File created.

接下來，您需要編輯，新增備用參數 pfile。 若要這麼做，請開啟 INITTEST。這個檔案的位置 %oracle\_常用 %\\資料庫。 接下來，新增下列陳述式 INITTEST.ora 檔案。 您初始化命名慣例。這個檔案是初始化\<YourDatabaseName\>。這個。

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


上一個陳述式區塊包含三個重要的設定項目︰
-   **LOG_ARCHIVE_CONFIG...:**您定義使用此陳述式的資料庫唯一識別碼。
-   **LOG_ARCHIVE_DEST_1...:**您定義本機的封存資料夾的位置使用此陳述式。 我們建議您建立新資料庫的封存需求的目錄，並指定明確地使用此陳述式，而不是使用 Oracle 的預設資料夾 %oracle_home%\database\archive 本機封存位置。
-   **LOG_ARCHIVE_DEST_2......LGWR 非同步︰**定義非同步記錄作者程序 (LGWR) 來收集交易取消復原資料，並將它傳給備用的目的地。 在這裡，DB_UNIQUE_NAME 指定目的地備用伺服器資料庫的唯一名稱。

新的參數檔案後，您需要從其建立 spfile。

首先，關閉資料庫︰

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

接下來，請執行啟動 nomount 命令，如下所示︰

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

現在，建立 spfile:

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

然後關閉資料庫︰

    SQL> shutdown immediate;

    ORA-01507: database not mounted

然後，使用 [啟動] 命令來啟動執行個體︰

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>建立實體備用資料庫
本節著重於您必須執行 Machine2 準備實體備用資料庫中的步驟。

首先，您需要遠端桌面 Machine2 透過 Azure 傳統入口網站。

然後，在備用伺服器 (Machine2) 上，建立所有必要的資料夾，備用資料庫，例如 c:\\\<YourLocalFolder\>\\測試。 時遵循此教學課程，請確定資料夾結構符合上 Machine1 保留所有必要的檔案，例如 controlfile、 資料檔案機、 redologfiles、 udump、 bdump 及 cdump 檔案的資料夾結構。 此外，定義 ORACLE\_家用版 ORACLE\_Machine2 中的基本環境變數。 如果不是，請將其定義為環境變數使用環境變數] 對話方塊。 若要存取此對話方塊，先**系統**公用程式按兩下 [系統] 圖示，在**[控制台]**。然後按一下 [**進階**] 索引標籤，然後選擇 [**環境變數**。 若要設定環境變數，請按一下 [**新增**] 按鈕底下的 [**系統變數**。 設定後的環境變數，您必須關閉現有的 Windows 命令提示字元，然後開啟新的項目，即可看到變更。

接下來，請遵循下列步驟︰

1. 準備備用資料庫的初始參數檔案

2. 設定接聽和 tnsnames 支援主要和備用電腦上的資料庫

    1. 如要保留項目的這兩個資料庫伺服器設定 listener.ora

    2. 如要保留項目的主要和備用資料庫的主要和備用虛擬機器上設定 tnsnames.ora

    3. 啟動接聽程式，並檢查兩個服務的兩個虛擬機器上 tnsping。

3. 啟動 nomount 狀態的備用執行個體

4. 若要複製的資料庫，並建立備用資料庫使用 RMAN

5. 在受管理的復原模式中啟動實際的備用資料庫

6. 驗證實體備用資料庫

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1.準備備用資料庫的初始參數檔案

本節將示範如何準備備用資料庫的初始參數檔案。 若要這麼做，請先複製 INITTEST。這個檔案從電腦 1 Machine2 以手動方式。 您應該能夠看到 INITTEST。這個檔案 %oracle\_常用 %\\資料庫在兩部電腦中的資料夾。 然後，修改即可設定所指定下方的備用角色 Machine2 INITTEST.ora 檔案︰

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


上一個陳述式區塊包含兩個重要的設定項目︰

-   **\*.LOG_ARCHIVE_DEST_1:**您需要在 Machine2 中手動建立 c:\OracleDatabase\TEST_STBY\archives 資料夾。
-   **\*.LOG_ARCHIVE_DEST_2:**這是選擇性的步驟。 您設定時可能會在主要的電腦中進行的維修作業，而備用電腦會變成主要的資料庫時需要。

然後，您需要啟動備用的執行個體。 在備用資料庫伺服器上，輸入下列命令以建立 Oracle 執行個體建立的 Windows 服務的 Windows 命令提示字元︰

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

[ **Oradim** ] 命令會建立 Oracle 執行個體，但不會啟動。 您可以找到它 c:\\OracleDatabase\\產品\\11.2.0\\dbhome\_1\\BIN 目錄。

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>設定接聽和 tnsnames 支援主要和備用電腦上的資料庫
建立備用資料庫之前，必須先確定您的設定的主要和備用資料庫可以與其他。 若要這麼做，您需要設定接聽和 TNSNames 手動或使用網路設定公用程式 NETCA。 當您使用 [復原管理員公用 (RMAN)，這是必要的工作。

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>如要保留項目的這兩個資料庫伺服器設定 listener.ora

遠端桌面 Machine1 和 listener.ora 指定的檔案為] 下方的編輯。 當您編輯 listener.ora 檔案時，請務必的左、 右括號對齊同一欄中。 您可以在下列資料夾中找到 listener.ora 檔案︰ c:\\OracleDatabase\\產品\\11.2.0\\dbhome\_1\\網路\\管理員\\。

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

接下來，遠端桌面 Machine2 並編輯 listener.ora 檔案，如下所示: # listener.ora 網路設定檔︰ C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>如要保留項目的主要和備用資料庫的主要和備用虛擬機器上設定 tnsnames.ora

遠端桌面 Machine1 和 tnsnames.ora 指定的檔案為] 下方的編輯。 您可以在下列資料夾中找到 tnsnames.ora 檔案︰ c:\\OracleDatabase\\產品\\11.2.0\\dbhome\_1\\網路\\管理員\\。

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

遠端桌面 Machine2 和編輯 tnsnames.ora 檔案，如下所示︰

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>啟動接聽程式，並檢查兩個服務的兩個虛擬機器上 tnsping。

開啟新的 Windows 命令提示字元，主要和備用虛擬機器中，然後執行下列陳述式︰

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>啟動 nomount 狀態的備用執行個體
若要將環境設定為支援備用資料庫備用虛擬機器 (MACHINE2)。

首先，手動將複製密碼檔案從主要的電腦 (Machine1) 到備用電腦 (Machine2)。 這是必要的**系統**密碼必須在兩部電腦上相同。

然後，開啟 Windows 命令提示字元中 Machine2，並設定環境變數指向待機資料庫，如下所示︰

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

接下來，請啟動待命資料庫 nomount 狀態，然後產生 spfile。

啟動資料庫︰

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>若要複製的資料庫，並建立備用資料庫使用 RMAN
您可以使用復原管理員公用程式 (RMAN)，才能建立實體備用資料庫的主要資料庫的備份。

遠端桌面的備用虛擬機器 (MACHINE2) 及執行 RMAN 公用程式]，來指定一個完整的連線字串的兩個目標 （主要資料庫，Machine1） 及輔助 （備用資料庫，Machine2） 執行個體。

>[AZURE.IMPORTANT] 請勿使用的作業系統驗證，因為沒有資料庫中的備用伺服器電腦尚未。

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>在受管理的復原模式中啟動實際的備用資料庫
本教學課程說明如何建立實體備用資料庫。 建立邏輯備用資料庫的詳細資訊，請參閱 Oracle 文件。

開啟 SQL\*加上的命令提示字元，並啟用備用虛擬機器或伺服器 (MACHINE2) 資料防衛隊，如下所示︰

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

當您開啟備用資料庫**裝載**模式，保存記錄檔中傳送持續並受管理的修復程序持續套用備用資料庫的記錄。 如此一來，可確保備用資料庫保持最新版本與主要資料庫。 請注意，備用資料庫無法存取在這段期間報告用途。

當您在**閱讀**模式開啟備用的資料庫時，封存記錄傳送持續。 但受管理的修復程序停駐點。 這會使備用資料庫會逐漸過時，直到繼續受管理的修復程序。 您可以用於報告用途存取備用的資料庫，在這段時間，但資料可能不會反映最新變更。

一般而言，我們建議您以**裝載**模式保留資料備用資料庫中最新版本的主要資料庫失敗時保留備用資料庫。 不過，您可以將在**閱讀**模式的備用資料庫，用於報告用途根據您的應用程式需求。 下列步驟說明如何啟用以唯讀模式使用 SQL 資料防衛隊\*加上︰

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>驗證實體備用資料庫
本節將示範如何確認可用性設定以系統管理員。

開啟 SQL\*加命令提示字元視窗，並檢查封存取消復原上待命虛擬機器 (Machine2) 記錄上︰

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

開啟 SQL * 加號 (Machine1) 的主要電腦上的命令提示字元視窗和切換記錄檔︰

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

核取封存取消復原記錄上待命虛擬機器 (Machine2):

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

檢查有任何中斷上待命虛擬機器 (Machine2):

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

另一種驗證方法可以是容錯移轉到備用資料庫，然後測試可以回復到主要資料庫。 若要啟動的主要資料庫備用資料庫，請使用下列陳述式︰

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

如果您有沒有啟用倒敘在原始的主要資料庫，建議您捨棄原始的主要資料庫並重新建立為備用資料庫。

我們建議您啟用在主要的倒敘資料庫及備用的資料庫。 發生錯誤後移轉種情況，就可以快閃回容錯移轉前的時間並快速地轉換到備用資料庫的主要資料庫。

##<a name="additional-resources"></a>其他資源
[Oracle Azure 虛擬機器圖像](virtual-machines-windows-classic-oracle-images.md)
