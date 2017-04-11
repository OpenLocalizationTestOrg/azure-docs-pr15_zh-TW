<properties
   pageTitle="從 SQL Server 到 Azure SQL 資料倉庫 (SSIS) 載入資料 |Microsoft Azure"
   description="顯示如何建立 SQL Server 整合服務 (SSIS) 套件，將 SQL Data Warehouse 從各種不同資料來源的資料。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>將資料載入從 SQL Server 到 Azure SQL 資料倉庫 (SSIS)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)


建立 SQL Server 整合服務 (SSIS) 套件，從 SQL Server 資料載入 Azure SQL Data Warehouse。 您也可以重建、 轉換，以及清除資料通過 SSIS 資料流。

在本教學課程中，您將會︰

- 在 Visual Studio 中建立新的 Integration Services 專案。
- 連線到資料來源，包括 （作為來源） 的 SQL Server 和 SQL Data Warehouse （作為目的地）。
- SSIS 封裝的設計載入資料來源的目的地。
- 執行載入資料的 SSIS 套件。

本教學課程中使用 SQL Server 作為資料來源。 無法執行 SQL Server，內部部署或 Azure 虛擬機器中。

## <a name="basic-concepts"></a>基本概念

封裝是 SSIS 工時單位。 相關的封裝一起專案。 您在使用 SQL Server Data Tools Visual Studio 中建立專案和設計套件。 設計程序是在其中您拖曳並從 [工具箱] 的元件放到設計介面、 連線，並設定其屬性的視覺程序。 完成您的套件之後，可以選擇性地將其部署到 SQL Server 全面涵蓋所有內容管理、 監控及安全性。

## <a name="options-for-loading-data-with-ssis"></a>載入使用 SSIS 資料的選項

SQL Server 整合服務 (SSIS) 是非常有彈性的一組工具的連線，以及將資料載入到 SQL Data Warehouse 提供各種不同的選項。

1. 連線至 SQL Data Warehouse 使用 ADO 網路目的地。 本教學課程中使用 ADO 網路目的地，因為它有最少的設定選項。
2. 連線至 SQL Data Warehouse 使用 OLE DB 目的地。 這個選項可能會提供比 ADO 網路目的稍微提升效能。
3. 使用 Azure Blob 上傳工作階段 Azure Blob 儲存體中的資料。 若要啟動將資料載入 SQL Data Warehouse Polybase 指令碼，然後使用 SSIS 執行 SQL 工作。 這個選項提供最佳效能，列於此處的三個選項。 若要上傳 Azure Blob 的工作，請下載[Microsoft SQL Server 2016 整合服務 Feature Pack 的 Azure][]。 若要進一步瞭解 Polybase，請參閱[PolyBase 指南][]。

## <a name="before-you-start"></a>在您開始之前

若要在逐步執行本教學課程中，您需要︰

1. **SQL Server 整合服務 (SSIS)**。 SSIS 的 SQL Server 元件，且需要試用版或授權的版本的 SQL Server。 若要取得評估版本的 SQL Server 2016 Preview，請參閱[SQL Server 評估][]。
2. **Visual Studio**。 若要取得免費 Visual Studio 2015 社群版，請參閱[Visual Studio 社群][]。
3. **SQL Server Data Tools for Visual Studio (SSDT)**。 SQL Server Data Tools for Visual Studio 2015，請參閱[下載 SQL Server Data Tools (SSDT)][]。
4. **範例資料**。 本教學課程中會載入 SQL Data Warehouse 使用存放在 SQL Server AdventureWorks 範例資料庫中的來源資料的範例資料。 若要取得 AdventureWorks 範例資料庫，請參閱[AdventureWorks 2014 範例資料庫][]。
5. **SQL Data Warehouse 資料庫與權限**。 本教學課程中連線至 SQL Data Warehouse 執行個體並將資料載入。 您必須具備權限來建立資料表和資料載入。
6. **防火牆規則**。 您必須防火牆規則 SQL Data Warehouse 上使用您的本機電腦的 IP 位址之前，先建立您上傳資料以 SQL Data Warehouse。

## <a name="step-1-create-a-new-integration-services-project"></a>步驟 1︰ 建立新的 Integration Services 專案

1. 啟動 [Visual Studio 2015。
2. 在 [**檔案**] 功能表上選取**新增 |專案**。
3. 瀏覽至**安裝 |範本 |商務智慧 |整合服務**專案類型。
4. 選取 [ **Integration Services 專案**]。 提供的值**名稱**和**位置**，然後再選取**[確定]**。

Visual Studio 隨即開啟，並建立新的整合服務 (SSIS) 專案。 Visual Studio 隨後單一新 SSIS 封裝 (Package.dtsx) 的設計工具中的專案。 您會看到以下的畫面區域︰

- 在左側，SSIS 元件的**工具箱]** 。
- 在中間，設計介面，使用多個索引標籤。 您通常會使用至少**控制項流程**以及**資料流程**] 索引標籤。
- 在右側，**方案總管] 中**，[**屬性**] 窗格。

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>步驟 2︰ 建立基本的資料流程

1. 資料流程工作從工具箱拖曳到設計介面的中心 （在 [**控制項流程**] 索引標籤）。

    ![][02]

2. 按兩下資料流程任務切換至 [資料流程] 索引標籤。
3. 從工具箱中的 [其他來源] 清單中，將 ADO.NET 來源拖曳到設計介面。 來源介面卡仍處於選取狀態， **SQL Server**來源**屬性**] 窗格中變更其名稱。
4. 從 [工具箱] 中的 [其他目的地] 清單中，拖曳到設計介面 ADO.NET 來源] 下的 ADO.NET 目的地。 目的地介面卡仍處於選取狀態，變更為**SQL DW 目的地**其名稱，在 [**屬性**] 窗格中。

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>步驟 3︰ 設定來源介面卡

1. 按兩下以開啟**ADO.NET 原始檔編輯器**的來源介面卡。

    ![][03]

2. 按一下 [**連線管理員**] 索引標籤的**ADO.NET 原始檔編輯器** **ADO.NET 連線管理員**清單，以開啟 [**設定 ADO.NET 連線管理員**] 對話方塊，然後建立本教學課程中將資料載入從 SQL Server 資料庫的連線設定] 旁的 [**新增**] 按鈕。

    ![][04]

3. 在 [**設定 ADO.NET 連線管理員**] 對話方塊中，按一下 [**新增**] 按鈕，開啟 [**連線管理員**] 對話方塊，並建立新的資料連線。

    ![][05]

4. 在 [**連線管理員**] 對話方塊中，執行下列項目。

    1. **提供者**，請選取 [SqlClient 資料提供者]。
    2. [**伺服器名稱**] 中，輸入 SQL Server 名稱。
    3. 在 [**登入伺服器**] 區段中，選取或輸入驗證資訊。
    4. 在 [**連線至資料庫**] 區段中，選取 AdventureWorks 範例資料庫。
    5. 按一下 [**測試連接**]。
    
        ![][06]
    
    6. 在報表的 「 連接測試結果的對話方塊中，按一下**[確定**] 以返回 [**連線管理員**] 對話方塊。
    7. 在 [**連線管理員**] 對話方塊中，按一下 [若要回到 [**設定 ADO.NET 連線管理員**] 對話方塊的**[確定**]。
 
5. 在 [**設定 ADO.NET 連線管理員**] 對話方塊中，按一下**[確定**] 以返回**ADO.NET 原始檔編輯器**。
6. 在**ADO.NET 原始檔編輯器**] 中**的資料表或檢視的名稱**] 清單中選取的**Sales.SalesOrderDetail**資料表。

    ![][07]

7. 按一下 [**預覽**] 以查看 [**預覽結果**] 對話方塊中的來源資料表中的資料的第一次 200 列]。

    ![][08]

8. 在 [**預覽結果**] 對話方塊中，按一下 [**關閉**返回**ADO.NET 原始檔編輯器**。
9. 在**ADO.NET 原始檔編輯器**中，按一下**[確定**] 完成設定資料來源。

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>步驟 4︰ 將來源介面卡連線至目的地介面卡

1. 選取來源介面卡設計介面上。
2. 選取延伸從來源介面卡的藍色箭號，然後將其拖曳至目的地編輯器，直到它貼齊位置。

    ![][10]

    在一般 SSIS 套件中，您可以使用其他元件，從 [SSIS 工具箱來源與目的地之間的數字重建、 轉換，及通過 SSIS 資料流程清除您的資料。 若要保留此範例中，只要可能，我們來源直接連接到目的地。

## <a name="step-5-configure-the-destination-adapter"></a>步驟 5︰ 設定目的地介面卡

1. 按兩下以開啟**ADO.NET 目的地編輯器**的目的地介面卡。

    ![][11]

2. 按一下 [ **ADO.NET 目的地編輯器**] 的 [**連線管理員**] 索引標籤中，開啟 [**設定 ADO.NET 連線管理員**] 對話方塊，並建立本教學課程中將資料載入到 Azure SQL Data Warehouse 資料庫的連線設定的 [**連線管理員**] 清單旁的 [**新增**] 按鈕。
3. 在 [**設定 ADO.NET 連線管理員**] 對話方塊中，按一下 [**新增**] 按鈕，開啟 [**連線管理員**] 對話方塊，並建立新的資料連線。
4. 在 [**連線管理員**] 對話方塊中，執行下列項目。
    1. **提供者**，請選取 [SqlClient 資料提供者]。
    2. [**伺服器名稱**] 中，輸入 SQL Data Warehouse 名稱。
    3. 在 [**登入伺服器**] 區段中，選取 [**使用 SQL Server 驗證**] 並輸入驗證資訊。
    4. 在 [**連線至資料庫**] 區段中，選取 [現有的 SQL Data Warehouse 資料庫]。
    5. 按一下 [**測試連接**]。
    6. 在報表的 「 連接測試結果的對話方塊中，按一下**[確定**] 以返回 [**連線管理員**] 對話方塊。
    7. 在 [**連線管理員**] 對話方塊中，按一下 [若要回到 [**設定 ADO.NET 連線管理員**] 對話方塊的**[確定**]。
5. 在 [**設定 ADO.NET 連線管理員**] 對話方塊中，按一下**[確定**] 以返回**ADO.NET 目的地編輯器**。
6. 在**ADO.NET 目的地編輯器**中，按一下 [開啟相符的來源資料表的資料行清單建立新的目的資料表的 [**建立表格**] 對話方塊的 [**使用資料表或檢視**] 清單旁的 [**新增**]。

    ![][12a]

7. 在 [**建立表格**] 對話方塊中，執行下列項目。

    1. 變更**SalesOrderDetail**目的資料表的名稱。
    2. 移除**rowguid**資料行。 SQL Data Warehouse 不支援的**唯一**資料類型。
    3. 變更**金錢** **LineTotal**欄的資料類型。 SQL Data Warehouse 不支援的**小數位數**的資料類型。 支援的資料類型的相關資訊，請參閱[建立表格 （Azure SQL Data Warehouse、 Parallel Data Warehouse）][]。
    
        ![][12b]
    
    4. 按一下**[確定]**以建立資料表，並返回 [ **ADO.NET 目的地編輯器**]。

8. 在**ADO.NET 目的地編輯器**中，選取 [若要查看如何在來源中的欄對應到資料行的目的地 [**對應**] 索引標籤。

    ![][13]

9. 按一下**[確定]**以完成設定資料來源]。

## <a name="step-6-run-the-package-to-load-the-data"></a>步驟 6︰ 執行的資料載入套件

按一下工具列上的 [**開始**] 按鈕，或選取 [**偵錯**] 功能表上的 [**執行**] 選項的其中一個，請執行套件。

當套件開始執行時，您會看到黃色的旋轉滾輪表示活動，以及處理到目前為止的資料列數目。

![][14]

當套件完成執行時，您會看到綠色的核取記號，以表示成功，以及從來源載入到目的地的資料列的總數。

![][15]

恭喜您 ！ 您已成功使用 SQL Server 整合服務，將資料載入 Azure SQL Data Warehouse。

## <a name="next-steps"></a>後續步驟

- 深入瞭解 SSIS 資料流。 從這裡開始︰[資料流][]。
- 瞭解如何為偵錯和疑難排解您套件中的向右設計環境。 從這裡開始︰[封裝開發的疑難排解工具][]。
- 瞭解如何部署 SSIS 專案及封裝整合服務的伺服器或其他的儲存位置。 從這裡開始︰[部署的專案與套件][]。

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[PolyBase 指南]: https://msdn.microsoft.com/library/mt143171.aspx
[下載 SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[建立表格 （Azure SQL Data Warehouse、 平行 Data Warehouse）]: https://msdn.microsoft.com/library/mt203953.aspx
[資料工作流程]: https://msdn.microsoft.com/library/ms140080.aspx
[封裝開發的疑難排解工具]: https://msdn.microsoft.com/library/ms137625.aspx
[專案和套件的部署]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Microsoft SQL Server 2016 整合服務的功能 Azure 的套件]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server 評估]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Visual Studio 社群]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[AdventureWorks 2014 範例資料庫]: https://msftdbprodsamples.codeplex.com/releases/view/125550
