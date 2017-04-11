<properties 
    pageTitle="Azure 虛擬機器上時，將資料移到 SQL Server |Azure" 
    description="將資料從一般檔案，或從內部部署的 SQL Server SQL Server Azure VM 上。" 
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
    ms.date="09/14/2016" 
    ms.author="bradsev" /> 

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Azure 虛擬機器上時，將資料移到 SQL Server

本主題概述將資料從一般檔案 （CSV 或 TSV 格式），或是從內部部署的 SQL Server 到 SQL Server Azure 虛擬機器上的選項。 將資料移至雲端的這些任務是小組資料科學程序的一部分。

主題會概述的電腦學習時，將資料移至 Azure SQL 資料庫的選項，請參閱[移動到 Azure 電腦學習 Azure SQL 資料庫的資料](machine-learning-data-science-move-sql-azure.md)。

下方的**功能表**主題說明如何將其他目標環境可以儲存及處理期間小組資料科學程序 (TDSP) 資料內嵌資料的連結。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


下表摘要列出資料移到 SQL Server，Azure 虛擬機器上的選項。

<b>來源</b> |<b>目標︰ SQL Server Azure VM 上</b> |
------------------ |-------------------- |
<b>一般檔案</b> |1。<a href="#insert-tables-bcp">命令列大量複製公用程式 (BCP)</a><br> 2。<a href="#insert-tables-bulkquery">大量插入 SQL 查詢</a><br> 3。<a href="#sql-builtin-utilities">在 SQL Server 中的圖形化內建公用程式</a>
<b>內部部署的 SQL Server</b> | 1。<a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">部署到 Microsoft Azure VM] 精靈的 SQL Server 資料庫</a><br> 2。<a href="#export-flat-file">匯出到一般檔案</a><br> 3。 <a href="#sql-migration">SQL 資料庫移轉精靈]</a> <br> 4。<a href="#sql-backup">資料庫後，設定與還原</a><br>

請注意，這份文件，假設從 SQL Server Management Studio 或 Visual Studio 資料庫檔案總管中執行 SQL 命令。

> [AZURE.TIP] 或者，您可以使用[Azure 資料工廠](https://azure.microsoft.com/services/data-factory/)建立和排程的管線會將資料移至 SQL Server VM Azure。 如需詳細資訊，請參閱[複製與 Azure 資料工廠 （複製活動） 的資料](../data-factory/data-factory-data-movement-activities.md)。


## <a name="prereqs"></a>必要條件
本教學課程，假設您有︰

* **Azure 訂閱**。 如果您沒有訂閱，您可以註冊[免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure 儲存體帳戶**。 將資料儲存在此教學課程中，您會使用 Azure 儲存體帳戶。 如果您沒有安裝 Azure 儲存體帳戶，請參閱[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)。 您建立的儲存空間帳戶後，您必須以取得帳戶金鑰] 用來存取儲存空間。 請參閱[檢視、 複製及重新產生的儲存空間便捷鍵](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
* 提供的**SQL Server Azure VM 上**。 如需相關指示，請參閱[設定為 IPython 筆記本伺服器的進階分析 Azure SQL Server 虛擬機器](machine-learning-data-science-setup-sql-server-virtual-machine.md)。
* 安裝和設定**PowerShell 的 Azure**本機。 如需相關指示，請參閱[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。


## <a name="filesource_to_sqlonazurevm"></a>將資料來源中的一般檔案移動到 SQL Server，Azure VM 上

如果您的資料是在一般檔案 （排列列/欄格式），它可以移到 SQL Server VM Azure 上透過以下兩種方法︰

1. [命令列大量複製公用程式 (BCP)](#insert-tables-bcp) 
2. [大量插入 SQL 查詢](#insert-tables-bulkquery)
3. [在 SQL Server （匯入/匯出、 SSIS） 中的圖形化內建公用程式](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>命令列大量複製公用程式 (BCP)

BCP 是安裝 SQL Server 的命令列公用，其中一個移動資料最快的方法。 其運作方式過所有三個 SQL Server 變化 （內部部署的 SQL Server 與 SQL Azure SQL Server VM Azure 上）。 

> [AZURE.NOTE]**我的資料應該儲存的 BCP？**  
> 雖然您不需要，將包含與目標 SQL server 同一部電腦上的來源資料的檔案可以讓更快速地傳輸 （網路速度與本機磁碟 IO 速度）。 您可以移動包含資料至電腦的一般檔案的位置使用各種不同的檔案複製工具，例如[AZCopy](../storage/storage-use-azcopy.md)來安裝 SQL Server， [Azure 儲存體總管](http://storageexplorer.com/)] 或 [windows 複製/貼上透過遠端桌面通訊協定 (RDP)。

1. 確定資料庫及表格建立目標 SQL Server 資料庫。 以下是如何使用範例`Create Database`和`Create Table`命令︰

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. 產生的說明表格的結構描述發出下列命令，從電腦上的命令列安裝 bcp 格式檔案。

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. 插入使用 bcp] 命令，如下所示的資料庫中的資料。 假設在同一部電腦上安裝 SQL Server 應該使用從命令列︰

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **最佳化 BCP 插入**請參閱下列文章最佳化這類隨後便會插入[「 最佳化大量匯入的指導方針 」](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) 。


### <a name="insert-tables-bulkquery-parallel"></a>更快速地移動資料的平行插入

如果您要移動的資料很大，您可以加速項目中的 PowerShell 指令碼的平行同時執行多個 BCP 命令。

> [AZURE.NOTE]**顯示較大資料 Ingestion**若要最佳化載入大型和大型資料集的資料，分割使用多個檔案群組和分割表格邏輯和實體資料庫資料表。 如需有關建立和載入至資料分割表格的資料的詳細資訊，請參閱[平行載入 SQL 磁碟分割表格](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。


下列範例 PowerShell 指令碼示範使用 bcp 平行插入︰
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>大量插入 SQL 查詢

[大量插入 SQL 查詢](https://msdn.microsoft.com/library/ms188365)可根據列/欄檔案 （涵蓋[準備資料大量匯出或匯入 (SQL Server)](https://msdn.microsoft.com/library/ms188609)中支援的類型） 從資料庫將資料匯入主題。 

以下是一些範例命令大量插入是如下︰  

1. 分析資料，並設定之前，請確定 SQL Server 資料庫假設相同的任何特殊的欄位，例如日期格式，匯入的任何自訂的選項。 若要將 [日期] 格式設定年的月份日 （如果您的資料包含年月日格式的日期） 的方式的範例如下︰

        SET DATEFORMAT ymd; 
    
2. 使用大量匯入資料匯入陳述式︰

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>在 SQL Server 中的內建公用程式

您可以在一般檔案從 Azure SQL Server VM 將資料匯入使用 SQL Server 整合服務 (SSIS)。 在兩個 studio 環境中使用 SSIS。 如需詳細資訊，請參閱[整合服務 (SSIS) 和 Studio 環境](https://technet.microsoft.com/library/ms140028.aspx)︰

- SQL Server Data Tools 的詳細資訊，請參閱[Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- 匯入/匯出精靈的詳細資訊，請參閱[SQL Server 匯入及匯出精靈](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>將資料從內部部署的 SQL Server 到 SQL Server Azure VM 上

您也可以使用下列移轉策略︰

1. [部署到 Microsoft Azure VM] 精靈的 SQL Server 資料庫](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [匯出到一般檔案](#export-flat-file) 
3. [SQL 資料庫移轉精靈]](#sql-migration)
4. [設定資料庫後，與還原](#sql-backup)

我們來描述每個下列︰

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>部署到 Microsoft Azure VM] 精靈的 SQL Server 資料庫

**部署到 Microsoft Azure VM] 精靈的 SQL Server 資料庫**是簡單且建議的方式，將資料從內部部署的 SQL Server 執行個體 SQL Server Azure VM 上。 如詳細的步驟，以及其他的討論，請參閱[移轉 Azure VM 上 SQL Server 資料庫](../virtual-machines/virtual-machines-windows-migrate-sql.md)。

### <a name="export-flat-file"></a>匯出到一般檔案

各種方法可以用於大量匯出資料從上內部部署的 SQL Server，如詳加說明[大量匯入及匯出的資料 (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx)主題。 這份文件將涵蓋大量複製程式 (BCP) 作為範例。 一旦資料匯出到一般檔案，可以匯入到另一個使用大量匯入的 SQL server。 

1. 內部部署的 SQL Server 資料匯出至檔案]，如下所示使用 bcp 公用程式

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. 建立資料庫，以及在 SQL Server VM Azure 使用`create database`和`create table`的表格結構描述匯出在步驟 1。

3. 建立格式來描述正在匯出/匯入資料的表格結構描述檔案。 [建立格式檔案 (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx)說明格式檔案的詳細資料。

    格式化檔案產生時從 SQL Server 的電腦執行 BCP 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    格式化檔案產生時執行 BCP 遠端 SQL Server 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. 若要將資料在一般檔案移到 SQL Server 中使用任何[移動檔案來源的資料](#filesource_to_sqlonazurevm)] 區段中所描述的方法。

### <a name="sql-migration"></a>SQL 資料庫移轉精靈]

[SQL Server 資料庫移轉精靈](http://sqlazuremw.codeplex.com/)提供兩個 SQL server 執行個體之間移動資料方便使用的方式。 其允許使用者對應來源和目的資料表之間的資料結構描述，請選擇欄類型和其他不同的功能。 它會使用大量複製 (BCP) 在幕後。 以下是 SQL 資料庫移轉精靈的 [歡迎] 畫面的螢幕擷取畫面。  

![SQL Server 移轉精靈]][2]

### <a name="sql-backup"></a>設定資料庫後，與還原

SQL Server 支援︰ 

1. [設定資料庫後，還原這項功能](https://msdn.microsoft.com/library/ms187048.aspx)（兩者都本機的檔案或 bacpac 匯出至 blob） 和 （使用 bacpac）[資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx)。 
2. 若要直接建立 SQL Server Vm Azure 上複製的資料庫或複製到現有的 SQL Azure 資料庫的能力。 如需詳細資訊，請參閱[使用複製的 [資料庫] 精靈](https://msdn.microsoft.com/library/ms188664.aspx)。 

螢幕擷取畫面的資料庫備份向上/還原從 SQL Server Management Studio 選項如下所示。

![SQL Server 匯入工具][1]

## <a name="resources"></a>資源

[SQL server Azure VM 上移轉資料庫](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server 上 Azure 虛擬機器概觀](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png
