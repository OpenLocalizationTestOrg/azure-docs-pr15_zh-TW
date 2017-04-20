<properties 
    pageTitle="平行大量資料匯入使用 SQL 磁碟分割表格 |Microsoft Azure" 
    description="使用 SQL 磁碟分割表格的平行大量資料匯入" 
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
    ms.date="09/19/2016" 
    ms.author="bradsev" /> 

# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>使用 SQL 磁碟分割表格的平行大量資料匯入

這份文件說明如何建置 fast 平行大量匯入的 SQL Server 資料庫的資料分割的表格。 大資料載入/傳輸時必須使用 SQL 資料庫，可以使用_分割表格與檢視_改進資料匯入 SQL 資料庫及後續的查詢。 


## <a name="create-a-new-database-and-a-set-of-filegroups"></a>建立新的資料庫與檔案群組的一組

- [建立新的資料庫](https://technet.microsoft.com/library/ms176061.aspx)（如果存在）
- 將資料庫檔案群組新增至的資料庫現在會保留已分割的實體檔案

  請注意： 達成此目的是[建立資料庫](https://technet.microsoft.com/library/ms176061.aspx)新如果或[ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx)如果資料庫已經存在

- 將一或多個檔案 （如有需要） 新增至每個資料庫檔案群組

 > [AZURE.NOTE] 指定含有此分割區的資料的目標檔案群組和實體資料庫檔案名稱儲存檔案群組資料。
 
下列範例會建立新的資料庫與三個以外的主要檔案群組包含的每一個實體檔案的記錄群組。 設定 SQL Server 執行個體中 [預設 SQL Server Data] 資料夾中，建立資料庫檔案。 如需預設檔案位置的詳細資訊，請參閱[預設與名為 SQL Server 的執行個體的檔案位置](https://msdn.microsoft.com/library/ms143547.aspx)。

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);
    
    EXECUTE ('
        CREATE DATABASE <database_name>
        ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
        FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
        LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')
    
## <a name="create-a-partitioned-table"></a>建立已分割的表格

建立已分割的資料結構描述，對應到在上一個步驟中建立的資料庫檔案群組依據的資料表。 大量匯入至已分割的資料表資料時，記錄將分散式之間分割區配置根據檔案群組，如下所示。

**若要建立的磁碟分割表格，您需要：**

- [建立分割函式](https://msdn.microsoft.com/library/ms187802.aspx)會定義值/界限如要包含的每個個別的磁碟分割表格、 限制以月為單位的分割區的範圍 (一些\_datetime\_欄位) 2013年年：

        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

- [建立分割區配置](https://msdn.microsoft.com/library/ms179854.aspx)以將分割函數中的每個分割區範圍對應至實體檔案群組，例如：

        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

  秘訣： 若要確認作用在根據函數/配置每個分割區中的範圍，執行下列查詢：

        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

- [建立已分割的表格](https://msdn.microsoft.com/library/ms174979.aspx)（s） 根據至您的資料結構描述，並指定用於分割表格中，例如的分割區配置和條件約束欄位：

        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

如需詳細資訊，請參閱[建立已分割的資料表和索引](https://msdn.microsoft.com/library/ms188730.aspx)。


## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>大量匯入每個個別的磁碟分割表格的資料

- 您可以使用 BCP、 大量插入，或其他方法例如[SQL Server 移轉精靈 」](http://sqlazuremw.codeplex.com/)。 所提供的範例使用 BCP 方法。

- 若要變更為 BULK_LOGGED 降至最低的記錄，例如負荷的交易記錄配置，[變更資料庫](https://msdn.microsoft.com/library/bb522682.aspx)：

        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- 若要加速載入的資料，啟動平行大量匯入作業。 以促進大量匯入 big 資料的 SQL Server 資料庫的秘訣，請參閱[載入 1 TB 的小於 1 小時](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)。

下列 PowerShell 指令碼會載入使用 BCP 平行資料的範例。

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0
    
    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>
       
    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"
   
    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir
      
    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }
    
    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }
    
    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }
    
    Get-Job
    
    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>建立要最佳化聯結及查詢效能的索引

- 如果您將會從多個資料表擷取資料模型 （英文）、 建立索引來改善加入效能加入機碼。

- [建立索引](https://technet.microsoft.com/library/ms188783.aspx)（叢集或非叢集） 目標為每個分割區的相同檔案群組的例如：

        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
或者，

        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] 您可以選擇建立大量資料匯入之前的索引。 大量匯入之前的索引建立會降低載入的資料。


## <a name="advanced-analytics-process-and-technology-in-action-example"></a>進階的分析程序與巨集指令範例中的技術

端對端逐步解說搭配使用範例 Cortana 分析程序公用資料集，請參閱[Cortana 分析的程序中巨集指令： 使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)。
 
