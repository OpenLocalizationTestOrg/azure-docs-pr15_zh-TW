<properties
    pageTitle="小組資料科學程序中︰ 使用 SQL Data Warehouse |Microsoft Azure"
    description="進階的分析程序與技術的實際操作"  
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>小組資料科學程序中︰ 使用 SQL Data Warehouse

在本教學課程中，我們引導您建立並部署電腦，學習使用 SQL Data Warehouse (SQL DW) 的模型可公開使用資料集- [NYC 計程車往返](http://www.andresmh.com/nyctaxitrips/)資料集。 二進位分類模型建構預測提示所支付的旅行，與 multiclass 分類和迴歸模型也討論的預測分配支付提示金額。

程序會遵循[小組資料科學程序 (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/)工作流程。 我們會示範如何設定資料科學環境，如何將資料載入到 SQL DW，以及如何使用 SQL DW 或 IPython 筆記本探索資料並工程功能模型。 然後，我們會示範如何建立並部署與 Azure 電腦學習的模型。


## <a name="dataset"></a>NYC 計程車往返資料集

NYC 計程車差旅費資料包含了關於的 20 GB 壓縮 CSV 檔案 (未壓縮 ~ 48 GB)，錄製超過 173 百萬個別往返與 fares 所支付的每個出差行程井然有序。 每個旅行記錄包含收取和下車位置與時間、 匿名駭客 （驅動程式的） 授權數字及 medallion （計程車的唯一識別碼） 數字。 資料在 2013年年涵蓋所有往返和每個月提供下列兩個資料集︰

1. **Trip_data.csv**檔案包含出差的詳細資訊，例如數乘客、 收取與 dropoff 點、 差旅費工期和旅行長度。 以下是一些範例記錄中︰

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. **Trip_fare.csv**檔案含有所支付的每個出差行程井然有序，例如付款類型、 fare 量、 銷售稅和稅金、 秘訣及 tolls，fare 和付款的詳細資料。 以下是一些範例記錄中︰

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

若要加入差旅費用的**唯一索引鍵**\_資料和旅行\_fare 由下列三個欄位所組成︰

- medallion，
- 駭客\_授權及
- 收取\_日期時間。

## <a name="mltasks"></a>地址預測工作的三個的類型

我們制定根據三個預測問題*提示\_量*以描繪出模型工作的三種︰

1. **二進位分類**︰ 預測是否提示付費旅行，亦即*提示\_量*大大於 $0 是正數的範例，時*提示\_量*$0 是負數的範例。

2. **Multiclass 分類**︰ 預測的提示所支付的範圍。 將*提示\_量*到五個區間或類別︰

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **迴歸工作**︰ 預測的旅行付費的秘訣。  


## <a name="setup"></a>設定進階分析 Azure 資料科學環境

若要設定您的 Azure 資料科學環境，請遵循下列步驟。

**建立您自己的 Azure blob 儲存體帳戶**

- 當您佈建自己 Azure blob 儲存體時，選擇您的 Azure blob 儲存體中的地理位置，或為關閉，可能**南部美國中部**，也就是 NYC 計程車儲存資料。 使用容器從公用 blob 儲存體容器 AzCopy 儲存帳戶中複製資料。 深入瞭解您 Azure blob 儲存體南部美國中部，即可將會更快完成這項工作 (步驟 4)。
- 若要建立您自己的 Azure 儲存體帳戶，請遵循在[相關 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)所述的步驟。 請務必備忘稿上下列的儲存空間帳戶認證的值會需要在此逐步解說稍後。

  - **儲存體帳戶名稱**
  - **儲存帳戶金鑰**
  - **容器名稱**（由您想要儲存在 Azure blob 儲存體中的資料）

**佈建 Azure SQL DW 執行個體。**
請依照[建立 SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)佈建 SQL Data Warehouse 執行個體的文件。 請確定您進行標記法，將會在稍後步驟中使用下列 SQL Data Warehouse 認證。

  - **伺服器名稱**︰ <server Name>。 database.windows.net
  - **SQLDW （資料庫） 的名稱**
  - **使用者名稱**
  - **密碼**

**安裝 Visual Studio 2015 和 SQL Server Data Tools。** 如需相關指示，請參閱[安裝 Visual Studio 2015 及/或的 SQL Data Warehouse SSDT (SQL Server Data Tools)](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)。

**連線至您的 SQL Azure DW Visual studio。** 如需相關指示，請參閱步驟 1 和 2，[連線到 Azure SQL Data Warehouse Visual studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md)中。

>[AZURE.NOTE] 執行下列 SQL 查詢 （而非連線主題的步驟 3 中所提供的查詢） 您 SQL Data Warehouse 中所建立的資料庫，建立**主索引鍵**。

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**建立下 Azure 訂閱 Azure 電腦學習工作區。** 如需相關指示，請參閱[建立 Azure 電腦學習工作區](machine-learning-create-workspace.md)。

## <a name="getdata"></a>將資料載入到 SQL Data Warehouse

開啟 Windows PowerShell 命令主控台。 執行下列動作的 PowerShell 命令，若要下載的範例 SQL 指令碼我們與您共用的 Github 本機您指定參數的目錄中的檔案*-DestDir*。 您可以變更的參數值*-DestDir*任何本機的目錄。 如果*-DestDir*不存在，它將會建立的 PowerShell 指令碼。

>[AZURE.NOTE] 如果您*DestDir*目錄需要建立或寫入的系統管理員權限，請執行下列 PowerShell 指令碼時，您可能需要以**管理員身分執行**。

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

後執行成功，您目前的工作目錄變更為*-DestDir*。 您應該能夠看到畫面下方等︰

![][19]

在您*-DestDir*中，執行下列管理員模式中的 PowerShell 指令碼︰

    ./SQLDW_Data_Import.ps1

當 PowerShell 指令碼的第一次執行時，系統會要求您輸入您 Azure SQL DW 與 Azure blob 儲存體帳戶中的資訊。 完成這個 PowerShell 指令碼時執行第一次，認證您輸入會已經寫入到簡報的工作目錄中的設定檔 SQLDW.conf。 未來執行這個 PowerShell 指令碼檔有讀取所有需要的參數，此設定檔中的選項。 如果您需要變更某些參數，您可以選擇以刪除此設定檔和正在輸入參數值，當系統提示您輸入時提示畫面上的參數，或編輯*-DestDir*目錄中的 SQLDW.conf 檔案變更的參數值。

>[AZURE.NOTE] 為了避免出現與其已存在於您 Azure SQL DW，直接從 SQLDW.conf 檔案閱讀參數時的結構描述名稱衝突，3 位數隨機數字會新增至結構描述名稱 SQLDW.conf 檔案的預設名稱為結構描述的每次執行。 PowerShell 指令碼可能會提示您輸入的結構描述名稱︰ 可能使用者選擇是否指定的名稱。

這個**PowerShell 指令碼**檔完成下列工作︰

- **下載並安裝 AzCopy**如果 AzCopy 尚未安裝

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **複製資料到您的私人 blob 儲存體帳戶**公用 blob 與 AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- 從私人 blob 儲存體帳戶使用下列命令，**您 Azure SQL DW 載入資料使用 Polybase （藉由執行 LoadDataToSQLDW.sql）** 。

    - 建立結構描述

            EXEC (''CREATE SCHEMA {schemaname};'');

    - 建立資料庫範圍認證

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - 建立外部資料來源的儲存空間 Azure blob

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - 建立外部的檔案格式的 csv 檔。 資料壓縮，而欄位會以直立線符號字元。

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - 建立外部 fare 和 NYC 計程車資料集的旅行資料表中 Azure blob 儲存體。

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - 從 Azure blob 儲存體中的外部表格資料載入 SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - 建立範例運算列表 (NYCTaxi_Sample)，然後選取差旅費和 fare 表格的 SQL 查詢以將其插入資料。 （此逐步解說一些步驟需要使用此範例資料表）。

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

您儲存的帳戶的地理位置會影響載入時間。

>[AZURE.NOTE] 根據您的私人 blob 儲存體帳戶的地理位置，從公用 blob 的資料複製到您私人的儲存空間的帳戶的程序可能需要 15 分鐘，或更久，及您儲存帳戶中的資料載入至您的 Azure SQL DW 的程序可能需要 20 分鐘或更長的時間。  

您必須決定哪些執行，如果您有重複的來源與目的地檔案。

>[AZURE.NOTE] 如果.csv 檔案複製至您的私人 blob 儲存體帳戶公用 blob 儲存體已存在於私人 blob 儲存體帳戶，AzCopy 會詢問您是否要將它們覆寫。 如果您不想它們覆寫，請輸入**n**出現提示時。 如果您想要覆寫**所有**的地方，輸入**一個**出現提示時。 您也可以輸入的**y**個別覆寫.csv 檔案。

![繪製 #21][21]

您可以使用您自己的資料。 如果您的資料是在您的內部部署電腦實際應用程式中，您還是可以使用 AzCopy 內部部署資料其上傳到您私人的 Azure blob 儲存體。 您只需要變更的**來源**位置， `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`，AzCopy 命令包含資料的本機目錄的 PowerShell 指令碼檔中。


>[AZURE.TIP] 如果您的資料是在您私人的 Azure blob 儲存體中實際應用程式中，您可以略過 AzCopy 步驟中的 PowerShell 指令碼，並直接上傳至 Azure SQL DW 的資料。 這需要額外的編輯的指令碼，使其符合您資料的格式。


這個 Powershell 指令碼也插入 Azure SQL DW 資訊中的資料探索範例檔案 SQLDW_Explorations.sql 與 SQLDW_Explorations_Scripts.py SQLDW_Explorations.ipynb，以便這三個檔案已準備好要嘗試立即 PowerShell 指令碼完成後。

執行成功之後，您會看到畫面下方等︰

![][20]

## <a name="dbexplore"></a>資料瀏覽功能工程和 Azure SQL Data Warehouse 中

在此區段中，執行資料探索及功能產生對 Azure SQL DW 直接使用**Visual Studio 資料工具**執行 SQL 查詢。 可以找到所有使用此區段中的 SQL 查詢中名為*SQLDW_Explorations.sql*範例指令碼。 此檔案已下載至您的本機目錄的 PowerShell 指令碼。 您也可以從[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql)擷取它。 但是 Github 中的檔案並沒有插入 Azure SQL DW 資訊。

開啟**SQL 物件總管]**以確認您的資料庫及表格都已匯入時，連線至 SQL DW 登入名稱和密碼使用 Visual Studio 您 Azure SQL DW。 擷取*SQLDW_Explorations.sql*檔案。

>[AZURE.NOTE] 若要開啟平行資料倉庫 (PDW) 查詢編輯器] 中，使用您 PDW **SQL 物件總管**] 中選取 [**新增查詢**] 命令。 PDW 不支援標準 SQL 查詢編輯器]。

以下是資料型別探索和功能產生工作執行此區段中︰

- 瀏覽不同的時間 windows 中的幾個欄位的資料發佈。
- 檢查資料品質經緯度欄位。
- 產生二進位和 multiclass 分類的標籤**提示\_量**。
- 產生功能，並計算比較差旅費距離。
- 加入兩個資料表，並擷取將用來建立模型的隨機範例。

### <a name="data-import-verification"></a>資料匯入驗證

這些查詢提供的列數快速驗證並填入先前使用 Polybase 的平行大量資料表中的資料匯入]

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**輸出︰**您應該取得 173,179,759 列和 14 的欄。

### <a name="exploration-trip-distribution-by-medallion"></a>探索︰ 出差發佈 medallion

此範例查詢識別在指定的期間內完成更多不要超過 100 個往返 medallions （計程車數字）。 查詢會因分割的資料表存取因為它條件的磁碟分割配置**收取\_datetime**。 查詢完整的資料集，就也能使用分割資料表及/或索引掃描。

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**輸出︰**查詢應傳回指定 13,369 medallions (taxis) 和差旅費由其完成 2013年中的數字的資料列的表格。 最後一個資料行包含往返完成的計數。

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>探索︰ 出差發佈 medallion 和 hack_license

此範例中識別 medallions （計程車數字），並 hack_license 數字 （驅動程式） 已完成的多個 100 往返在指定的期間內。

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**輸出︰**查詢應傳回表格 13,369 列指定 13,369 汽車/驅動程式已經完成更多的 100 往返 2013年中的識別碼。 最後一個資料行包含往返完成的計數。

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>資料品質評量︰ 驗證不正確的經度及/或緯度的記錄

此範例檢查如果任何經度及/或緯度欄位的 [包含無效的值 （弧度的角度應該 90 和 90 之間），或有 （0，0） 座標。

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**輸出︰**此查詢會傳回 837,467 往返含有無效的經度及/或緯度欄位。

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>探索︰ 雪人與不 （雪人） 的之間的通訊群組

此範例中找出的程序所雪人與數字的不雪人中指定的時間內 （或如果以下設定好涵蓋完整西元年份的完整資料集）。 此分配會反映稍後使用的二進位分類模型的二進位標籤分配。

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**輸出︰**查詢傳回下列提示頻率年 2013年: 90,447,622 雪人和 82,264,709 不雪人。

### <a name="exploration-tip-classrange-distribution"></a>探索︰ 提示類別/範圍通訊群組

此範例會計算分配的提示範圍中指定的時間範圍 （或如果涵蓋完整西元年份的完整資料集）。 這是將會用於稍後 multiclass 分類模型標籤類別的通訊群組。

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**輸出︰**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>探索︰ 計算，並且比較差旅費距離

此範例轉換收取和下車經度並緯度 SQL 地理位置、 計算差旅費距離使用 SQL 地理位置的點差異，與傳回比較的結果的隨機樣本。 範例結果限制為僅使用舊版涵蓋資料品質評估查詢的有效座標。

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>使用 SQL 函數的功能工程

有時候 SQL 函數可能會有效功能工程選項。 在此逐步解說，我們已定義之 SQL 函數來計算的收取和 dropoff 位置的直接距離。 您可以在**Visual Studio 資料工具] 中**，執行下列 SQL 指令碼。

以下是定義距離函數的 SQL 指令碼。

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

以下是範例呼叫此函數來產生 SQL 查詢中的功能︰

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**輸出︰**此查詢會產生列的資料表 （2,803,538） 收取和 dropoff 經緯度和與您該英哩中的相對應直接距離。 以下是第一次 3 列的結果︰

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>準備模型建立的資料

下列查詢連接**nyctaxi\_差旅費**和**nyctaxi\_fare**資料表時，會產生二進位分類標籤**雪人**，多個類別分類標籤**提示\_類別**，及起完整聯結的資料集的範例。 取樣是由擷取根據收取時間往返子集。  此查詢可以複製，然後直接在[Azure 電腦學習 Studio](https://studio.azureml.net) [匯入資料]中貼上[import-data]適用於從 Azure SQL 資料庫執行個體的直接資料 ingestion 模組。 查詢排除不正確的記錄 （0，0） 座標。

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

當您準備好繼續 Azure 電腦學習時，您可以︰  

1. 儲存最後擷取範例資料和複製貼上查詢直接將[匯入資料]的 SQL 查詢[import-data]模組中 Azure 電腦學習，或
2. 固定您計劃用於建立新的 SQL DW 資料表中的模型，並使用新資料表[匯入資料]的取樣和工程資料[import-data]中 Azure 電腦學習的模組。 在先前的步驟中的 PowerShell 指令碼具有此您完成。 您可以閱讀直接從 [匯入資料] 模組中的資料表。


## <a name="ipnb"></a>資料探索和 IPython 筆記本中的功能工程

在此區段中，我們會執行資料探索和使用兩個 Python 功能產生，並針對 SQL DW 的 SQL 查詢建立較舊版本。 「 **SQLDW_Explorations.ipynb** Python 指令碼檔**SQLDW_Explorations_Scripts.py**範例 IPython 筆記本已下載到您的本機目錄。 他們也可在[GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)上。 這兩個檔案都相同 Python 指令碼。 Python 指令碼檔被提供給您，以防您沒有 IPython 筆記本伺服器。 這兩個範例 Python 檔案是**Python 2.7**底下所設計。

視需要的 Azure SQL DW 中的資訊樣本 IPython 筆記本和 Python 指令碼檔案下載至您的本機電腦具有已插入電源的 PowerShell 指令碼先前。 他們的年齡可執行檔不需要任何修改。

如果您已經有設定 AzureML 工作區，您就可以直接上傳至 AzureML IPython 筆記本服務的範例 IPython 筆記本，並開始執行。 以下是上傳至 AzureML IPython 筆記本服務的步驟︰

1. 登入您的 AzureML 工作區 「 Studio 」 頂端，按一下左側的 [網站] 頁面上的 「 筆記本 」。

    ![繪製 #22][22]

2. 左下角的網頁上，按一下 [新增]，然後選取 「 Python 2 」。 然後，提供筆記本的名稱，然後按一下核取記號，若要建立新的空白 IPython 筆記本。

    ![繪製 #23][23]

3. 按一下新 IPython 筆記本的左上角的 「 Jupyter 」 符號。

    ![繪製 #24][24]

4. 拖曳並拖放至您 AzureML IPython 筆記本的服務的**樹狀目錄**頁面範例 IPython 筆記本，按一下 [**上傳**。 然後，將上傳 AzureML IPython 筆記本服務範例 IPython 筆記本。

    ![繪製 #25][25]

若要執行此範例 IPython 筆記本或 Python 指令碼檔案，下列封包所需的 Python。 如果您使用的 AzureML IPython 筆記本服務，這些套件已預先安裝。

    - 熊
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

在 AzureML 建置進階分析的方案，使用大型資料時的建議程序如下︰

- 在小的範例資料的讀取將記憶體中的資料範圍。
- 執行某些視覺效果和 explorations 使用取樣的資料。
- 嘗試使用功能工程使用取樣的資料。
- 如需較大的資料探索、 資料操作與功能工程，使用 Python 提出 SQL 查詢直接 SQL DW。
- 決定以適合 Azure 電腦學習模型建置樣本大小。

下列是幾個資料探索、 資料視覺效果和工程範例的功能。 更多資料 explorations 可以找到範例 IPython 筆記本和範例 Python 指令碼檔中。

### <a name="initialize-database-credentials"></a>初始化資料庫認證

初始化您資料庫中的連線設定下列變數︰

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>建立資料庫的連線

以下是建立資料庫的連線的連接字串。

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>報表的資料列和欄數 < nyctaxi_trip > 表格中

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- 資料列的總數 = 173179759  
- 資料行總數 = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>報表的資料列和欄數 < nyctaxi_fare > 表格中

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- 資料列的總數 = 173179759  
- 資料行總數 = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>閱讀在從 SQL 資料倉庫資料庫的小型資料範例

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

若要閱讀範例資料表為 14.096495 秒的時間。  
擷取的列和欄數 = (1000年 21)。

### <a name="descriptive-statistics"></a>敘述統計

現在您已經準備好探索取樣的資料。 我們開始位於的一些敘述統計**差旅費\_距離**（或您選擇要指定任何其他欄位]）。

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>視覺效果:] 方塊中繪圖範例

接下來我們看看呈現 quantiles 在出差距離] 方塊中繪圖。

    df1.boxplot(column='trip_distance',return_type='dict')

![繪製 #1][1]

### <a name="visualization-distribution-plot-example"></a>視覺效果︰ 分配繪圖範例

以視覺化方式呈現的分配和取樣的差旅費距離的直方圖的繪圖。

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![繪製 #2][2]

### <a name="visualization-bar-and-line-plots"></a>視覺效果︰ 橫條圖和折線圖繪圖

在此範例中，我們區間到五個區間的旅行距離，並以視覺化方式呈現 binning 的結果。

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

我們可以繪製上述回收筒分配列或行與繪圖︰

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![繪製 #3][3]

與

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![繪製 #4][4]

### <a name="visualization-scatterplot-examples"></a>視覺效果︰ Scatterplot 範例

我們會示範之間的散佈圖**差旅費\_時間\_中\_秒**和**差旅費\_距離**若要查看是否有任何關聯性

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![繪製 #6][6]

同樣地，我們可以檢查之間的關聯性**工資率\_程式碼**和**差旅費\_距離**。

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![繪製 #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>取樣資料使用 IPython 筆記本中的 SQL 查詢的資料瀏覽

在此區段中，我們會探索資料分配，使用取樣保存我們先前所建立的新資料表中的資料。 請注意，可以使用原始表格執行類似 explorations。

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>探索︰ 報表取樣資料表中的欄和列的數字

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>探索︰ 不雪人達到通訊群組

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>探索︰ 提示課程通訊群組

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>探索︰ 繪製課程提示通訊群組

    tip_class_dist['tip_freq'].plot(kind='bar')

![繪製 #26][26]


#### <a name="exploration-daily-distribution-of-trips"></a>探索︰ 輸入每日的通訊群組的存取

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>每個 medallion 探索︰ 出差通訊群組

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>探索︰ 出差分配 medallion 和駭客授權

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>探索︰ 來回時間通訊群組

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>探索︰ 出差距離通訊群組

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>探索︰ 付款類型通訊群組

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>驗證 featurized 表格的最後一個表單

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>建立模型中 Azure 電腦學習

我們已準備好要前往模型建置並中[Azure 電腦學習](https://studio.azureml.net)的模型部署。 資料可以用於任何也就是先前定義的預測問題︰

1. **二進位分類**︰ 預測是否提示付費的出差行程井然有序。

2. **Multiclass 分類**︰ 預測提示支付，根據先前定義的類別的範圍。

3. **迴歸工作**︰ 預測的旅行付費的秘訣。  



若要開始在模型作業，登入您的**Azure 電腦學習**工作區。 如果您尚未建立學習工作區的電腦，請參閱[建立 Azure 毫升工作區](machine-learning-create-workspace.md)。

1. 若要開始使用 Azure 電腦學習，請參閱[什麼是 Azure 電腦學習 Studio？](machine-learning-what-is-ml-studio.md)

2. [Azure 機器學習 Studio](https://studio.azureml.net)登入。

3. Studio 首頁模組參考，及其他資源提供豐富的資訊、 視訊、 教學課程的連結。 如需有關 Azure 電腦學習的詳細資訊，請參閱[Azure 電腦學習文件中心](https://azure.microsoft.com/documentation/services/machine-learning/)。

一般訓練實驗包含下列步驟︰

1. 建立 [ **+ 新增**實驗。
2. Azure 毫升將取得資料。
3. 預先處理、 轉換，視操作的資料。
4. 如有需要請產生功能。
5. 將資料分割成訓練/驗證/測試資料集 (或有不同的資料集的每一個)。
6. 選取一或多個電腦學習演算法根據解決學習問題。 例如，二進位分類 multiclass 分類、 迴歸分析。
7. 訓練使用訓練資料集的一或多個模型。
8. 分數使用訓練的模型驗證資料集。
9. 評估模型，來計算的度量學習問題的相關資訊。
10. 要微調模型，然後選取 [要部署的最佳模型。

這個練習中，我們已有探索和工程 SQL Data Warehouse 中的資料和 Azure 毫升中內嵌決定上樣本大小。 若要建立一或多個預測模型程序如下︰

1. 取得資料至使用[匯入資料]的 Azure 毫升[import-data]模組，請在 [**資料輸入與輸出**] 區段中使用。 如需詳細資訊，請參閱[匯入資料][import-data]模組參考] 頁面。

    ![Azure 毫升匯入資料][17]

2. 選取作為**資料來源****屬性**] 面板中的 [ **Azure SQL 資料庫**]。

3. 在 [**資料庫伺服器名稱**] 欄位中，輸入資料庫 DNS 名稱。 格式︰`tcp:<your_virtual_machine_DNS_name>,1433`

4. 在對應的欄位中輸入**資料庫名稱**。

5. 在輸入*SQL 使用者名稱***伺服器的使用者帳戶名稱**和*密碼*在**伺服器的使用者帳戶密碼**。

6. 核取 [**接受任何伺服器憑證**] 選項。

7. 在**資料庫查詢**編輯文字區域中，貼上擷取必要查詢資料庫欄位 （包括任何的計算的欄位，例如標籤） 及 [下移] 範例資料，以所要的樣本大小。

閱讀直接從 SQL Data Warehouse 資料庫的資料分類二進位實驗的範例會在下圖中 （記得要取代的資料表名稱 nyctaxi_trip 及 nyctaxi_fare 結構描述名稱，並在您的逐步解說中所使用的資料表名稱）。 類似的實驗可以用來建構 multiclass 分類和迴歸問題。

![Azure 毫升訓練][10]

> [AZURE.IMPORTANT] 資料模型中擷取及取樣查詢範例提供在前一節中，**在查詢中包含的三個模型練習的所有標籤**。 （必要） 的重要步驟，在每一個模型練習是**排除**不需要兩個問題，及任何其他**目標遺漏**標籤。 例如使用二進位分類時, 使用**傾斜**的標籤，並排除欄位**提示\_類別**，**提示\_量**，及**總\_量**。 第二個是目標遺漏，因為它們表示秘訣付款。
>
> 若要排除任何不需要的欄或目標遺漏，您可以使用 [[選取資料集內的資料行][select-columns]模組或[編輯的中繼資料][edit-metadata]。 如需詳細資訊，請參閱[在資料集中的 [選取資料行][select-columns]和[編輯的中繼資料][edit-metadata]參照頁面。

## <a name="mldeploy"></a>部署模型中 Azure 電腦學習

當您的模型時，可以輕鬆地將其部署直接從實驗的 web 服務。 如需有關部署 Azure 毫升 web 服務的詳細資訊，請參閱[部署 Azure 電腦學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

若要部署新的 web 服務，您需要︰

1. 建立計分的實驗。
2. 部署 web 服務。

若要從**已經完成**訓練實驗建立計分的實驗，按一下 [**建立計分嘗試**下方的 [動作] 列中。

![Azure 分數][18]

Azure 電腦學習會嘗試建立計分實驗，根據訓練實驗的元件。 將特別是︰

1. 儲存訓練的模型，並移除模型訓練模組。
2. 找出要代表預期輸入的資料結構描述的邏輯**輸入連接埠**。
3. 找出要代表預期的 web 服務輸出結構描述的邏輯**輸出連接埠**。

建立計分實驗時，請檢閱，然後進行視需要調整。 一般的調整是取代輸入的資料集及/或查詢的排除標籤欄位，如這些會時無法使用的服務要求。 也是很好的做法縮小輸入資料集及/或查詢至幾個記錄，就足以表示輸入結構描述。 輸出連接埠是常見排除所有輸入的欄位，只在輸出中使用[資料集中的 [選取資料行]中包含**計分標籤**和**計分機率**[select-columns]模組。

下圖中提供的範例計分實驗。 準備好部署中，按一下 [**發佈 WEB 服務**] 按鈕下方的 [動作] 列中。

![Azure 毫升發佈][11]


## <a name="summary"></a>摘要
重點複習我們已完成此逐步解說教學課程中，您已經建立 Azure 資料科學環境中，使用大型的公用資料集，將它完成小組資料科學程序，從資料擷取模型訓練課程，然後至 Azure 電腦學習 web 服務的部署。

### <a name="license-information"></a>授權資訊

此範例逐步解說和其伴隨的指令碼和 IPython notebook(s) 由 Microsoft 之下共用 MIT 授權。 請目錄中的範例程式碼上 GitHub，如需詳細資訊，在檢查 LICENSE.txt 檔案。

## <a name="references"></a>參照

• [Andrés Monroy NYC 計程車往返下載頁面](http://www.andresmh.com/nyctaxitrips/)  
•[阻撓 Chris Whong NYC 的計程車差旅費資料](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC 計程車 Limousine 佣金參考資料和統計資料](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
