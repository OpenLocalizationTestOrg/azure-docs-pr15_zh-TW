<properties
    pageTitle="連線到 SQL 資料庫使用 Python |Microsoft Azure"
    description="將呈現可用來連線到 Azure SQL 資料庫的 Python 程式碼範例。"
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-python"></a>使用 Python 連線至 SQL 資料庫


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


本主題說明如何連接及查詢的使用 Python Azure SQL 資料庫。 您可以從 Windows、 Ubuntu Linux 或 Mac 的平台來執行這個範例。


## <a name="step-1-create-a-sql-database"></a>步驟 1︰ 建立 SQL 資料庫

請參閱[快速入門] 頁面](sql-database-get-started.md)，瞭解如何建立範例資料庫。  請務必在您依照建立**資料庫範本的 AdventureWorks**指南。 使用**AdventureWorks 結構描述**所示的範例。 一旦您建立您的資料庫進行確認您啟用存取至您的 IP 位址啟用防火牆規則，如下所述的[快速入門頁面](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>步驟 2︰ 設定的開發環境

### <a name="mac-os"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>安裝所需的模組
開啟您 terminal，然後安裝

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

開啟您 terminal，然後瀏覽至您計劃用建立您 python 指令碼的目錄。 輸入下列命令以安裝**FreeTDS**和**pymssql**。 pymssql 使用 FreeTDS 連線至 SQL 資料庫。

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="windows"></a>**Windows**

從 [[**以下**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql)安裝 pymssql。 

請確定您選擇正確的 whl 檔案。 例如︰ 如果您使用的 Python 2.7 64 位元電腦上選擇 [: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl。 一旦您下載.whl 檔案放在 C/Python27 資料夾。

立即安裝使用從命令列 pip pymssql 驅動程式。 cd 插入 C/Python27] 和 [執行下列動作
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

您可以找到相關指示，才能使用 pip[以下](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>步驟 3︰ 執行程式碼範例

建立一個稱為**sql_sample.py**檔案，並貼上下列的程式碼內。 您可以執行此命令列使用︰
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>連線到您的 SQL 資料庫

[Pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html)函數用來連線到 SQL 資料庫。

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>執行選取的 SQL 陳述式

[Cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute)函數可以用於擷取結果集查詢的 SQL 資料庫。 此函數基本上接受任何查詢，並傳回的結果集可以反覆[cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone)使用。


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>插入列與傳遞的參數，擷取產生的主索引鍵

SQL 資料庫的[身分識別](https://msdn.microsoft.com/library/ms186775.aspx)屬性和[順序](https://msdn.microsoft.com/library/ff878058.aspx)物件可以用來自動產生[的主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx)的值。 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>交易


此程式碼範例會示範如何使用中的交易您︰

* 開始交易
* 插入一列的資料
* 復原您的交易復原插入 

貼上下列的程式碼 sql_sample.py 內。
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>後續步驟

* 檢閱[SQL 資料庫開發概觀](sql-database-develop-overview.md)
* [Microsoft SQL Server 的 Python 驅動程式](https://msdn.microsoft.com/library/mt652092.aspx)的詳細資訊
* 請造訪[Python 開發人員中心](/develop/python/)。

## <a name="additional-resources"></a>其他資源 

* [Azure SQL 資料庫的多租用戶 SaaS 應用程式的設計模式](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* 瀏覽所有的[SQL 資料庫的功能](https://azure.microsoft.com/services/sql-database/)
