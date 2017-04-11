<properties
   pageTitle="如何建立 SQL Data Warehouse 支援票證 |Microsoft Azure"
   description="如何建立 Azure SQL Data Warehouse 支援票證。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>如何建立 SQL Data Warehouse 支援票證
 
如果您有任何問題您 SQL Data Warehouse，請建立支援票證，讓我們工程團隊可協助您。

## <a name="create-a-support-ticket"></a>建立支援票證

1. 開啟[Azure 入口網站][]。

2. 在 [常用] 畫面中，按一下 [**說明 + 支援**磚。

    ![說明 + 支援](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. 從 [協助 + 支援刀，按一下 [**建立支援要求**。

    ![新支援要求](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. 選取 [**要求類型**]。

    ![要求類型](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  根據預設，每個 SQL server (例如 myserver.database.windows.net) 會有 45,000 **DTU 配額**。 此配額是只要安全性限制。 您可以藉由建立支援票證並選取要求類型的*配額*增加您配額。 若要計算您 DTU 必須，7.5 乘[DWU][]所需的總計。 例如，您想要主控一個 SQL server 上的兩個 DW6000s，然後您應該要求 90000 DTU 配額。  您可以在入口網站檢視您目前的 DTU 消耗從 SQL server 刀。 暫停和取消暫停資料庫計算至 DTU 配額。 

5. 選取**訂閱**裝載在您回報此問題的資料庫。

    ![訂閱](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. 您可以選取 [ **SQL Data Warehouse**為資源。

    ![資源](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. 選取您的[Azure 支援的方案][]。

    - 使用所有的支援層級**帳單管理配額與訂閱**支援。
    - 透過[開發人員][]、[標準][]、[專業直接][]或[首要][]支援提供**符號修正**支援。 中斷修正問題的客戶使用 Azure 時遇到問題合理的期望 Microsoft 造成問題的地方。
    - **開發人員顧問****諮詢服務**可使用及[專業直接][]和[首要][]支援層級。 
    
    如果您有進階支援的方案，您也可以回報 SQL Data Warehouse 相關問題，在[Microsoft 進階線上入口網站][]。  若要進一步瞭解各種支援計劃，包括範圍、 回應時間、 價格等，請參閱[Azure 支援方案][Azure 支援的方案]。 Azure 常見問題的支援，請參閱[Azure 支援的常見問題集][]。  

    ![支援計劃](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. 選取 [**問題類型**和**類別**。

    ![問題類型] 類別](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. 用來說明問題，然後選擇 [企業影響的層級。

    ![問題描述](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. 您**的連絡資訊**此支援票證會預先填滿。 如有必要，請更新此選項。

    ![連絡人資訊](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. 按一下 [**建立**提交支援要求。


## <a name="monitor-a-support-ticket"></a>監視器支援票證

您已經提交支援要求之後，Azure 支援小組會與您連絡。 若要檢查您的要求的狀態和詳細資料，請按一下 [儀表板上的 [**管理支援要求**]。

![檢查狀態](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>其他資源

此外，您可以連線與 SQL Data Warehouse 社群[堆疊溢位][]或[Azure SQL 資料倉庫 MSDN 論壇][]上。

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Azure 入口網站]: https://portal.azure.com/
[Azure 支援計劃]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[開發人員]: https://azure.microsoft.com/support/plans/developer/  
[標準]: https://azure.microsoft.com/support/plans/standard/  
[專業郵遞]: https://azure.microsoft.com/support/plans/prodirect/  
[進階]: https://azure.microsoft.com/support/plans/premier/  
[Azure 支援的常見問題集]: https://azure.microsoft.com/support/faq/
[Microsoft 進階線上入口網站]: https://premier.microsoft.com/
[堆疊溢位]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL 資料倉庫 MSDN 論壇]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

