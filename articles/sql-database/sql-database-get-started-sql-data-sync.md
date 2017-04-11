<properties
    pageTitle="快速入門 SQL 資料庫資料同步處理"
    description="本教學課程中，可協助您開始使用 Azure SQL 資料同步處理 （預覽版本）。"
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>快速入門 Azure SQL 資料同步處理 （預覽版本）
在此教學課程中，您將瞭解 Azure SQL 資料同步處理使用 Azure 傳統入口網站的基本工作。

本教學課程假設最小的先前曾與 SQL Server Azure SQL 資料庫。 在本教學課程中，您可以建立完全設定，並在您設定的排程同步處理的混合式 （SQL Server 和 SQL 資料庫執行個體） 同步處理群組。

> [AZURE.NOTE] 使用.pdf 為 Azure SQL 資料同步處理，前身為位於 MSDN，完成技術文件。 請[在這裡](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。

## <a name="step-1-connect-to-the-azure-sql-database"></a>步驟 1︰ 連線至 Azure SQL 資料庫

1. [傳統的入口網站](http://manage.windowsazure.com)登入。

2. 按一下左窗格中的 [ **SQL 資料庫**]。

3. 按一下 [**同步處理**底部的頁面]。 當您按一下同步處理時，出現的清單顯示的項目您可以新增為**新的同步處理群組**和**新的同步處理代理程式**。

4. 若要啟動新的 SQL 資料同步處理代理程式精靈，請按一下 [**新的同步處理代理程式**]。

5. 如果您尚未新增代理程式之前，**按一下 [在此下載**。

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>步驟 2︰ 新增用戶端代理程式
您會移至有您同步處理] 群組中包含內部部署的 SQL Server 資料庫時，才需要此步驟。 如果您的同步處理群組有只是 SQL 資料庫執行個體，請跳至步驟 4。

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>步驟 2a︰ 安裝所需的軟體
請確定您已安裝用戶端代理程式的電腦上安裝下列。

- **.NET framework 4.0**

 從 [[以下](http://go.microsoft.com/fwlink/?linkid=205836)安裝.NET Framework 4.0。

- **Microsoft SQL Server 2008 R2 SP1 系統 CLR 類型 (x86)**

 從 [[以下](http://www.microsoft.com/download/en/details.aspx?id=26728)安裝 Microsoft SQL Server 2008 R2 SP1 系統 CLR 類型 (x86)

- **Microsoft SQL Server 2008 R2 SP1 共用管理物件 (x86)**

 從 [[以下](http://www.microsoft.com/download/en/details.aspx?id=26728)安裝 Microsoft SQL Server 2008 R2 SP1 共用管理物件 (x86)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>步驟 2b︰ 安裝新的用戶端代理程式

遵循指示進行[安裝用戶端代理程式 （SQL 資料同步處理）](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)安裝代理程式。



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>步驟 2 c︰ 完成新 SQL 資料同步處理代理程式精靈

1.  返回新 SQL 資料同步處理代理程式精靈。
2.  授與代理程式有意義的名稱。
3.  從下拉式清單中，選取的**區域**（資料中心） 裝載這個代理程式。
4.  從下拉式清單中，選取 [**訂閱**裝載這個代理程式]。
5.  按一下 [向右箭號。



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>步驟 3︰ 登錄用戶端代理程式的 SQL Server 資料庫

用戶端代理程式安裝之後，註冊您想要包含的代理程式的同步處理] 群組中的每個內部部署 SQL Server 資料庫。
若要註冊代理程式的資料庫，請依照在[註冊 SQL Server 資料庫與用戶端代理程式](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)的指示進行。



## <a name="step-4-create-a-sync-group"></a>步驟 4︰ 建立同步處理的群組


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>步驟匣 4a︰ 開始新的同步處理群組精靈

1.  返回[標準入口網站](http://manage.windowsazure.com)。
2.  按一下 [ **SQL 資料庫]**。
3.  按一下 [**新增同步處理**底部的頁面]，然後從抽屜選取新的同步處理群組]。

    ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>步驟 4b︰ 輸入基本設定


1.  輸入有意義的名稱的 [同步處理] 群組。
2.  從下拉式清單中，選取的**區域**（資料中心） 裝載此同步處理] 群組。
3. 按一下 [向右箭號。

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>步驟 4 c︰ 定義同步處理中心

1. 從下拉式清單中，選取要做為同步處理群組中樞的 SQL 資料庫執行個體。
2. 針對此 SQL 資料庫執行個體-**中樞的使用者名稱**和**中心密碼**輸入認證。
3. SQL 資料同步處理]，確認使用者名稱和密碼，請等候。 您會看到綠色的核取記號會出現在右邊的密碼時都已確認認證。
4. 從下拉式清單中，選取 [**衝突解決程序**原則]。

 **中樞贏得**-寫入參照資料庫中心資料庫寫入的任何變更，覆寫變更，在同一個參照資料庫的記錄。 功能，這表示寫入中樞的第一個變更傳播到其他的資料庫。


 **用戶端贏得**-寫入中樞的變更會覆寫參照資料庫中的變更。 功能，這表示寫入中心的上一個變更是一個保留，而且傳播到其他資料庫。

5.  按一下 [向右箭號。

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>步驟 4 個 d︰ 新增參照資料庫


每個您想要新增至 [同步處理] 群組中的其他資料庫重複此步驟。

1. 從下拉式清單中，選取要新增的資料庫。

    在下拉式清單中的資料庫包含兩個已註冊的代理程式和 SQL 資料庫執行個體的 SQL Server 資料庫。
2.  此資料庫的**使用者名稱**和**密碼**，請輸入認證。
3.  從下拉式清單中，選取 [**同步處理方向**此資料庫]。

    **雙向**參照資料庫中的變更會寫入到中樞資料庫，然後中心資料庫的變更會寫入到參考資料庫。

    **從 [中心的同步處理**資料庫從中樞接收更新。 它不會傳送到中樞的變更。

    **同步處理到中樞**資料庫會傳送到中樞更新。 在中樞的變更不會寫入到這個資料庫。

4.  若要完成建立 [同步處理] 群組中，按一下精靈的右下角的核取記號。 SQL 資料同步處理]，確認認證，請等候。 綠色核取指出認證會加以確認。

5.  按一下第二個時間] 核取記號。 這會傳回 SQL 資料庫下的 [**同步處理**] 頁面。 此同步處理群組現在會列出使用其他同步處理群組和代理程式。

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>步驟 5︰ 定義同步處理資料

Azure SQL 資料同步處理可讓您選取資料表和要同步處理的資料行。 如果您也想要篩選資料行，因此只有資料列的特定值 (例如，年齡 > = 65) 同步處理、 Azure 在使用 SQL 資料同步處理入口網站和文件選取表格、 欄及列同步處理] 以定義資料同步處理。

1.  返回[標準入口網站](http://manage.windowsazure.com)。
2.  按一下 [ **SQL 資料庫]**。
3.  按一下 [**同步處理**] 索引標籤。
4.  按一下此同步處理群組的名稱。
5.  按一下 [**同步處理規則**] 索引標籤。
6.  選取您想要提供的同步處理] 群組中的結構描述的資料庫。
7.  按一下 [向右箭號。
8.  按一下 [**重新整理結構描述**]。
9.  每個資料庫資料表中，選取要包含在同步處理中的資料行。
    - 無法選取不受支援的資料類型的資料欄。
    - 如果不選取表格中的任何資料行，表格會不會包含 [同步處理] 群組中。
    - 若要選取/取消選取所有的資料表，請按一下 [選取] 畫面的底部。
10. 按一下 [**儲存**]，然後完成佈建的 [同步處理] 群組，請等候。
11. 若要返回資料同步處理登陸頁面，按一下 [上一步] 箭號 （上方同步處理群組的名稱） 的螢幕左上角。

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>步驟 6︰ 設定您的同步處理群組

您永遠可以同步同步處理群組，即可同步處理資料同步處理登陸頁面底部。
若要同步處理的排程，您可以設定 [同步處理] 群組。

1.  返回[標準入口網站](http://manage.windowsazure.com)。
2.  按一下 [ **SQL 資料庫]**。
3.  按一下 [**同步處理**] 索引標籤。
4.  按一下此同步處理群組的名稱。
5.  按一下 [**設定**] 索引標籤。
6.  **自動同步處理**
    - 若要設定同步處理設定頻率上的 [同步處理] 群組中，按一下 [**開啟**]。 您仍然可以即可同步處理同步處理要求。
    - 按一下 [**關閉**]，以設定同步處理只有在您按一下 [同步處理時的 [同步處理] 群組。
7.  **同步處理頻率**
    - 在 [自動同步處理時，設定同步處理頻率。 頻率必須是 5 分鐘介於 1 個月。
8.  按一下 [**儲存**]。

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

恭喜。 您建立了包含 SQL 資料庫執行個體和 SQL Server 資料庫的同步處理群組。

## <a name="next-steps"></a>後續步驟
如需有關 SQL 資料庫和 SQL 資料同步處理，請參閱的詳細資訊︰

* [下載完整的 SQL 資料同步處理技術文件](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [SQL 資料庫概觀](sql-database-technical-overview.md)
* [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
 

 
