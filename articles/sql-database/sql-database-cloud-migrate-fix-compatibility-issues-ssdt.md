<properties
   pageTitle="修正之前移轉到 SQL 資料庫的 SQL Server 資料庫相容性問題 |Microsoft Azure"
   description="SQL Azure 移轉精靈，SSDT、 Microsoft Azure SQL 資料庫，資料庫移轉、 相容性"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-sql-server-data-tools-for-visual-studio"></a>將 SQL Server 資料庫移轉到使用 SQL Server Data Tools for Visual Studio Azure SQL 資料庫 

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [升級指導](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

在本文中，您學習偵測並修正使用的 SQL Server Data Tools for Visual Studio 之前移轉到 Azure SQL 資料庫的 SQL Server 資料庫相容性問題。

## <a name="using-sql-server-data-tools-for-visual-studio"></a>使用 SQL Server Data Tools for Visual Studio

用於 SQL Server Data Tools for Visual Studio (「 SSDT 」) 的資料庫結構描述匯入的分析 Visual Studio 資料庫專案。 若要分析，您可以為 SQL 資料庫 V12 指定專案的目標平台，然後建立 [專案。 如果建立成功，是相容的資料庫。 如果建立失敗，您可以解決 SSDT （或在本主題中所討論的其他工具） 中的錯誤。 後專案建置完成，您可以將其發佈回為來源資料庫的複本。 然後，您可以使用 SSDT 中的資料比較功能將從來源資料庫的資料複製到 Azure SQL V12 相容資料庫。 然後，您可以移轉此更新的資料庫。 若要使用此選項，請下載[最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)。

  ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

  > [AZURE.NOTE] 如果需要只有結構描述的移轉，可以直接從 Visual Studio 發佈結構描述，直接到 Azure SQL 資料庫。 資料庫結構描述需要更多非單獨移轉精靈可處理的變更時，請使用這個方法。

## <a name="detecting-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>偵測使用 SQL Server Data Tools for Visual Studio 的相容性問題
   
1.  Visual Studio 中開啟**SQL Server 物件總管]** 。 使用**新增 SQL Server**連線至內含要移轉的資料庫的 SQL Server 執行個體。 尋找資料庫中物件總管資料庫，以滑鼠右鍵按一下，選取 [**建立新專案**     
    
    ![新的專案](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)    
   
2.  設定以**匯入應用程式範圍物件只能**匯入設定。 取消核取 [匯入下列選項︰ 參照登入、 權限及資料庫的設定。    

    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)    

3.  按一下 [**開始**] 以匯入的資料庫，並建立包含 T SQL 指令碼檔資料庫中的每個物件的專案。 在專案中的資料夾中的指令碼檔是巢狀。    

    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)    

4.  在 Visual Studio 方案總管中，以滑鼠右鍵按一下資料庫專案，然後選取 [內容]。 在 [**專案設定**] 頁面上設定 Microsoft Azure SQL 資料庫 V12 目標平台。    
    
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)    
    
5.  以滑鼠右鍵按一下專案，然後選取 [**建立**]，以建立專案。    
    
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)    
    
6.  **錯誤清單**會顯示每個不相容。 在此情況下，使用者名稱 NT AUTHORITY\NETWORK 服務不相容。 因為它是不相容，您可以查看註解或將其移除 （和地址的含意資料庫方案中移除此登入與角色）。     
    
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)    
    
## <a name="fixing-compatibility-issues-using-sql-server-data-tools-for-visual-studio"></a>使用 SQL Server Data Tools for Visual Studio 修正相容性問題

1.  按兩下以開啟的查詢] 視窗和註解指令碼，指令碼的第一個指令碼，然後執行指令碼]。     
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

2.  每個指令碼包含不相容，直到不到錯誤會保留重複此程序。    
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
    
3.  免費錯誤的資料庫時，以滑鼠右鍵按一下專案，然後選取 [**發佈]**。 內建和發佈來源資料庫的複本 （建議使用複本，至少一開始）。     
 - 在發佈之前，取決於來源 SQL Server 版本 （早於 SQL Server 2014），您可能需要重設專案的目標平台來啟用部署。     
 - 如果您要移轉的較舊版本的 SQL Server 資料庫，並不會引入任何功能不支援的 SQL Server 的來源專案之前，請到較新版本的 SQL Server 移轉資料庫。     

        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)    
    
        ![alt text](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)    
        
4.  在 SQL Server 物件總管] 中，以滑鼠右鍵按一下您在來源資料庫，按一下 [**資料比較**。 比較專案原始的資料庫，可協助您瞭解精靈做了哪些變更。 選取您 Azure SQL V12 版本的資料庫，然後再按一下 [**完成**]。    
    
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)    
    
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)    

5.  檢閱偵測到的差異，然後按一下 [**更新目標**，將資料從來源資料庫移轉至 Azure SQL V12 資料庫。     
    
    ![替代文字](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)    
    
6.  選擇 [部署方法]。 請參閱[相容的 SQL Server 資料庫移轉到 SQL 資料庫。](sql-database-cloud-migrate.md)  

## <a name="next-steps"></a>後續步驟

- [最新版本的 SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [最新版本的 SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>其他資源

- [SQL 資料庫 V12](sql-database-v12-whats-new.md)
- [TRANSACT-SQL 部分或不支援的函數](sql-database-transact-sql-information.md)
- [移轉非 SQL Server 資料庫時使用 SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)
