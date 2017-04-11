<properties
    pageTitle="資料複製到 SQL 資料庫 Blob 儲存體 |Microsoft Azure"
    description="本教學課程教您如何使用 Azure 資料工廠管道的複製活動，將資料複製到 SQL 資料庫 Blob 儲存體。"
    Keywords="blob sql，blob 儲存體中，複製資料"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>資料複製到 SQL 資料庫使用資料工廠 Blob 儲存體 
> [AZURE.SELECTOR]
- [概觀與先決條件](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [複製精靈](data-factory-copy-data-wizard-tutorial.md)
- [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Azure 資源管理員範本](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


在本教學課程中，您可以建立資料工廠的管線，將資料複製到 SQL 資料庫 Blob 儲存體。

複製活動 Azure 資料工廠執行資料移動。 它已開啟電源，可以將資料安全、 可靠且可調整方式的各種資料儲存區之間複製全域可用服務。 如需複製活動詳細資訊，請參閱[資料移動活動](data-factory-data-movement-activities.md)文章。  

> [AZURE.NOTE] 如詳細資料工廠服務的概觀，請參閱[Azure 資料工廠簡介](data-factory-introduction.md)一文。

##<a name="prerequisites-for-the-tutorial"></a>教學課程的先決條件
本教學課程之前，您必須具備下列項目︰

- **Azure 訂閱**。  如果您沒有安裝訂閱，您可以建立的免費的試用帳戶在幾分鐘。 請參閱[免費試用版](http://azure.microsoft.com/pricing/free-trial/)的詳細資料。
- **Azure 儲存體帳戶**。 您使用 blob 儲存體**來源**資料儲存區在本教學課程。 如果您沒有安裝 Azure 儲存體帳戶，請參閱[建立儲存的帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)的步驟，即可建立筆記本。
- **Azure SQL 資料庫**。 在本教學課程 Azure SQL 資料庫作為**目的地**資料存放區中。 如果您沒有安裝 Azure SQL 資料庫，您可以使用教學課程中，請參閱[建立及設定 Azure SQL 資料庫的方式](../sql-database/sql-database-get-started.md)來建立一個。
- **SQL Server 2012/2014年或 Visual Studio 2013**。 您使用 SQL Server Management Studio 或 Visual Studio 建立範例資料庫，以及檢視資料庫中的結果資料。  

## <a name="collect-blob-storage-account-name-and-key"></a>收集 blob 儲存體帳戶名稱和索引鍵 
您需要帳戶名稱和 Azure 儲存體帳戶執行本教學課程中的 [帳戶金鑰。 請注意下**客戶名稱**] 和 [Azure 儲存體帳戶的**[帳戶金鑰]** 。

1. [Azure 入口網站](https://portal.azure.com/)登入。
2. 在左功能表上按一下 [**更多服務**，然後選取**儲存的帳戶**。

    ![瀏覽-儲存帳戶](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. 在**儲存帳戶**刀中，選取您想要在本教學課程中使用**Azure 儲存體帳戶**。
4. 選取 [**設定**] 下的**便捷鍵**連結]。
5.  按一下 [**儲存體帳戶名稱**的文字] 方塊旁的 [**複製**（圖像）] 按鈕及儲存/貼上該位置 (例如︰ 文字檔案中)。
6. 重複上述步驟，[複製] 或 [向下**key1**筆記。
    
    ![儲存便捷鍵](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. 按一下 [ **X**] 關閉所有刀。

## <a name="collect-sql-server-database-user-names"></a>收集 SQL server 資料庫、 使用者名稱
您需要 Azure SQL server、 資料庫及執行本教學課程中的使用者的名稱。 請注意下 Azure SQL 資料庫**伺服器**、**資料庫**，以及**使用者**的名稱。

1. 在**Azure 入口網站**中，按一下左側的 [**更多服務**，然後選取 [ **SQL 資料庫**。
2. 在**SQL 資料庫刀**中，選取您想要在本教學課程中使用**資料庫**。 請注意下的**資料庫名稱**。  
3. 在**SQL 資料庫**刀中，按一下 [**設定**底下的 [**屬性**]。
4. 請注意下值的**伺服器名稱**] 和 [**伺服器管理員登入**。
5. 按一下 [ **X**] 關閉所有刀。

## <a name="allow-azure-services-to-access-sql-server"></a>允許存取 SQL server Azure 服務 
確定 [****允許存取 Azure 服務**設定開啟 Azure SQL server，讓資料工廠服務可以存取您的 Azure SQL server** 。 若要確認並開啟這個設定，請執行下列步驟︰

1. 按一下 [**更多服務**中心左側，然後按一下 [ **SQL server**。
2. 選取您的伺服器，然後按一下 [**設定**] 底下的 [**防火牆**]。 
4. 在**防火牆設定**刀中，按一下 [**允許存取 Azure 服務**的 [**開啟**]。
5. 按一下 [ **X**] 關閉所有刀。

## <a name="prepare-blob-storage-and-sql-database"></a>準備 Blob 儲存體及 SQL 資料庫 
現在，準備您的 Azure blob 儲存體和 Azure SQL 資料庫教學課程，執行下列步驟︰  

1. 啟動 [記事本]，貼上下列的文字，並將其儲存為**emp.txt** **C:\ADFGetStarted**資料夾中，在您硬碟上。

        John, Doe
        Jane, Doe

2. 若要建立**adftutorial**容器，並**emp.txt**檔案上傳至容器，請使用工具，例如[Azure 儲存檔案總管](https://azurestorageexplorer.codeplex.com/)。

    ![Azure 儲存體總管]。 資料複製到 SQL 資料庫 Blob 儲存體](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. 若要建立**emp**資料表 Azure SQL 資料庫中使用下列 SQL 指令碼。  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **如果您有 SQL Server 2012/2014 在電腦上安裝︰**遵循指示從[步驟 2︰ 連線至 SQL 資料庫管理 Azure SQL 資料庫的使用 SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2)連線到您 Azure SQL server，並執行 SQL 指令碼的文章。 本文使用[傳統 Azure 入口網站](http://manage.windowsazure.com)，不[新增 Azure 入口網站](https://portal.azure.com)，以設定 Azure SQL server 的防火牆。

    如果您的用戶端不允許存取 Azure SQL server，必須先設定 [允許存取您的電腦 （IP 位址） 從 Azure SQL server 的防火牆。 請參閱[本文](../sql-database/sql-database-configure-firewall-settings.md)的步驟，設定防火牆 Azure SQL server。

您已完成的必要條件。 您可以建立資料工廠使用下列方法之一。 按一下其中一個索引標籤的頂端或執行教學課程下列連結。     

- [Azure 入口網站](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [複製精靈](data-factory-copy-data-wizard-tutorial.md)
