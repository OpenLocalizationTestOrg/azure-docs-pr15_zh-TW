<properties
    pageTitle="進階分析的案例的程序與技術 Azure 機器學習 |Microsoft Azure"
    description="選取適當的案例的進階小組資料科學程序的預測狀況分析的實際操作。"
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


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>進階分析中 Azure 電腦學習的案例

本文概述了各種的範例資料來源及目標案例可處理的[小組資料科學程序 (TDSP)](data-science-process-overview.md)。 TDSP 提供小組進行共同作業建置智慧型應用程式的系統化的方法。 此處所提供的案例說明資料處理工作流程中可用的選項取決於資料特性、 來源位置和 Azure 中的目標存放庫。

**決策樹**選取範例案例適用於您的資料與目標所示的最後一節。

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


每個下列各節提供範例案例。 針對每個案例的資料可能科學或進階的分析流程和支援 Azure 資源所列。

>[AZURE.NOTE]**的所有下列情況中，您需要︰**
><br/>
>* [建立儲存的帳戶](../storage/storage-create-storage-account.md)
><br/>
>* [建立 Azure 電腦學習工作區](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>案例\#1︰ 小中型表格中的資料集的本機檔案

![小型中的本機檔案][1]

#### <a name="additional-azure-resources-none"></a>其他 Azure 資源︰ 無

1.  [Azure 機器學習 Studio](https://studio.azureml.net/)登入。

2.  上傳資料集。

3.  建立開始上傳的資料集 Azure 電腦學習實驗流程。

## <a name="smalllocalprocess"></a>案例\#2︰ 小型中的資料集的需要處理的本機檔案

![小型中處理的本機檔案][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 資源︰ Azure 虛擬機器 （IPython 筆記本伺服器）

1.  建立執行 IPython 筆記本 Azure 虛擬機器。

2.  將資料的上傳到 Azure 存放容器。

3.  預先處理程序，並清除 [在 IPython 筆記本中，從 Azure 存放容器中存取資料的資料。

4.  將資料轉換為清除，列表方式。

5.  儲存 Azure blob 的轉換資料。

6.  [Azure 機器學習 Studio](https://studio.azureml.net/)登入。

7.  從 Azure blob 使用[匯入資料]讀取資料[import-data]模組。

8. 建立 ingested 資料集開始著手 Azure 電腦學習實驗流程。

## <a name="largelocal"></a>案例\#3︰ 大型資料集的目標 Azure Blob 的本機檔案

![大型的本機檔案][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 資源︰ Azure 虛擬機器 （IPython 筆記本伺服器）

1.  建立執行 IPython 筆記本 Azure 虛擬機器。

2.  將資料的上傳到 Azure 存放容器。

3.  預先處理程序，並清除 [在 IPython 筆記本中，從 Azure blob 存取資料的資料。

4.  如有需要請轉換要清除，表格式資料。

5.  探索資料，並視需要建立功能。

6.  擷取小型-中型資料樣本。

7.  在 [Azure blob 儲存取樣的資料。

8. [Azure 機器學習 Studio](https://studio.azureml.net/)登入。

9. 從 Azure blob 使用[匯入資料]讀取資料[import-data]模組。

10. 建立 Azure 電腦學習實驗流程開始 ingested 資料集。


## <a name="smalllocaltodb"></a>案例\#4︰ 小型中的資料集的目標 SQL Server Azure Virtal 機器中的本機檔案

![小型中型中 Azure SQL 資料庫的本機檔案][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源︰ Azure 虛擬機器 (SQL Server / IPython 筆記本伺服器)

1.  建立執行 SQL Server + IPython 筆記本 Azure 虛擬機器。

2.  將資料的上傳到 Azure 存放容器。

3.  預先處理程序，並清除 [使用 IPython 筆記本的 Azure 存放容器中的資料。

4.  如有需要請轉換要清除，表格式資料。

5.  將資料儲存至 VM 本機檔案 （IPython 筆記本是在上執行 VM、 本機磁碟機參考 VM 磁碟機）。

6.  載入到 Azure VM 上執行的 SQL Server 資料庫的資料。

    選項\#1︰ 使用 SQL Server Management Studio 中。

    - 登入 SQL Server VM
    - 執行 SQL Server Management Studio 中。
    - 建立資料庫與目的資料表。
    - 使用其中一個大量匯入的資料載入從 VM 本機檔案的方法。

    選項\#2︰ 使用 IPython 筆記本 – 不建議您的中型和大型資料集<!-- -->    
    - 若要存取 VM 上 SQL Server 使用 ODBC 連線字串。
    - 建立資料庫與目的資料表。
    - 使用其中一個大量匯入的資料載入從 VM 本機檔案的方法。

7.  探索資料，視需要建立功能。 請注意，不需要進行實體化資料庫資料表中的功能。 僅限注意建立所需的查詢。

8. 如果有需要及/或想要，依據資料樣本大小。

9. [Azure 機器學習 Studio](https://studio.azureml.net/)登入。

10. 直接從使用[匯入資料]的 SQL Server 讀取資料[import-data]模組。 貼上所需的查詢會擷取欄位、 建立功能，並範例資料，如有需要直接在[匯入資料][import-data]查詢。

11. 建立 Azure 電腦學習實驗流程開始 ingested 資料集。

## <a name="largelocaltodb"></a>案例\#5︰ 在本機的檔案中的大型資料集目標 Azure VM 中的 SQL Server

![大型中 Azure SQL 資料庫的本機檔案][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源︰ Azure 虛擬機器 (SQL Server / IPython 筆記本伺服器)

1.  建立執行 SQL Server 和 IPython 筆記本 server Azure 虛擬機器。

2.  將資料的上傳到 Azure 存放容器。

3.  （選用）預先處理程序，讓資料更簡潔。

    。  預先處理程序，並清除 [在 IPython 筆記本中，從 Azure blob 存取資料的資料。

    b。  如有需要請轉換要清除，表格式資料。

    c。  將資料儲存至 VM 本機檔案 （IPython 筆記本是在上執行 VM、 本機磁碟機參考 VM 磁碟機）。

4.  載入到 Azure VM 上執行的 SQL Server 資料庫的資料。

    。  登入 SQL Server VM。

    b。  如果資料不已儲存，請從 Azure 存放容器下載資料檔案，到本機 VM 資料夾。

    c。  執行 SQL Server Management Studio 中。

    d。  建立資料庫與目的資料表。

    e。  使用其中一個大量匯入的資料載入的方法。

    f。  如果需要資料表聯結，則可建立索引來加速連接。

     > [AZURE.NOTE] 更快速地載入大型資料大小，建議您建立分割的資料表，並大量匯入平行中的資料。 如需詳細資訊，請參閱[平行 SQL 分割表格資料匯入](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。

5.  探索資料，視需要建立功能。 請注意，不需要進行實體化資料庫資料表中的功能。 僅限注意建立所需的查詢。

6.  如果有需要及/或想要，依據資料樣本大小。

7.  [Azure 機器學習 Studio](https://studio.azureml.net/)登入。

8. 直接從使用[匯入資料]的 SQL Server 讀取資料[import-data]模組。 貼上所需的查詢會擷取欄位、 建立功能，並範例資料，如有需要直接在[匯入資料][import-data]查詢。

9. 開始使用上傳的資料集的簡單 Azure 電腦學習實驗流程

## <a name="largedbtodb"></a>案例\#6︰ 在 SQL Server 資料庫內部部署，針對選取目標 SQL Server Azure 虛擬機器中的大型資料集

![大型 SQL DB 內部部署中 Azure SQL 資料庫][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源︰ Azure 虛擬機器 (SQL Server / IPython 筆記本伺服器)

1.  建立執行 SQL Server 和 IPython 筆記本 server Azure 虛擬機器。

2.  使用其中一個資料匯出至 SQL Server 資料匯出傾印檔案的方法。

    > [AZURE.NOTE] 如果您決定要移動的所有資料從內部部署的資料庫，移至 Azure 中的 SQL Server 執行個體的 [完整的資料庫 （更快速地） 替代方法。 略過步驟匯出資料、 建立資料庫，並載入/匯入資料至目標資料庫及追蹤的替代方法。

3.  將傾印檔案上傳至 Azure 存放容器。

4.  將資料載入到 SQL Server 資料庫執行 Azure 虛擬機器上。

    。  登入 SQL Server VM。

    b。  從 Azure 存放容器的資料檔案下載至本機 VM 資料夾中。

    c。  執行 SQL Server Management Studio 中。

    d。  建立資料庫與目的資料表。

    e。  使用其中一個大量匯入的資料載入的方法。

    f。  如果需要資料表聯結，則可建立索引來加速連接。

    > [AZURE.NOTE] 更快速的載入大型資料大小，建立分割的資料表並大量匯入平行中的資料。 如需詳細資訊，請參閱[平行 SQL 分割表格資料匯入](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。

5.  探索資料，視需要建立功能。 請注意，不需要進行實體化資料庫資料表中的功能。 僅限注意建立所需的查詢。

6.  如果有需要及/或想要，依據資料樣本大小。

7.  [Azure 機器學習 Studio](https://studio.azureml.net/)登入。

8. 直接從使用[匯入資料]的 SQL Server 讀取資料[import-data]模組。 貼上所需的查詢會擷取欄位、 建立功能，並範例資料，如有需要直接在[匯入資料][import-data]查詢。

9. 簡單的 Azure 電腦學習嘗試開始上傳的資料集的流程。

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>若要從內部部署的 SQL Server 的完整資料庫複製到 Azure SQL 資料庫的替代方法

![卸離本機資料庫，並將附加到 SQL Azure 中的資料][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>其他 Azure 資源︰ Azure 虛擬機器 (SQL Server / IPython 筆記本伺服器)

若要複製整個 SQL Server 資料庫在您的 SQL Server VM 中，您應該資料庫從某個位置/伺服器複製到另一個假設的資料庫可以離線暫時。 在 [SQL Server 管理 Studio 物件總管]，或使用相等的 SQL 命令執行此動作。

1. 中斷連結的來源位置的資料庫。 如需詳細資訊，請參閱[卸離資料庫](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx)。
2. 在 Windows 檔案總管或 Windows 命令提示字元視窗中，複製到 SQL Server VM Azure 中的目標位置的中斷連結的資料庫或檔案和記錄檔的檔案。
3. 將複製的檔案附加至的目標 SQL Server 執行個體。 如需詳細資訊，請參閱[附加資料庫](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx)。

[移動資料庫使用卸離和附加 (TRANSACT-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>案例\#7︰ 在本機的檔案中的大型資料目標 Azure HDInsight Hadoop 叢集的登錄區資料庫

![在本機的目標登錄區中的大型資料][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>其他 Azure 資源︰ Azure HDInsight Hadoop 叢集和 Azure 虛擬機器 （IPython 筆記本伺服器）

1.  建立執行 IPython 筆記本 server Azure 虛擬機器。

2.  建立 Azure HDInsight Hadoop 叢集。

3.  （選用）預先處理程序，讓資料更簡潔。

    。  預先處理程序，並清除 [在 IPython 筆記本中，從 Azure blob 存取資料的資料。

    b。  如有需要請轉換要清除，表格式資料。

    c。  將資料儲存至 VM 本機檔案 （IPython 筆記本是在上執行 VM、 本機磁碟機參考 VM 磁碟機）。

4.  Hadoop 叢集在步驟 2 中選取的預設容器上, 傳的資料。

5.  載入到 Azure HDInsight Hadoop 叢集登錄區資料庫的資料。

    。  登入 Hadoop 叢集主節點

    b。  開啟 Hadoop 命令列。

    c。  以輸入登錄區根目錄`cd %hive_home%\bin`Hadoop 命令列中的商務連絡人。

    d。  執行登錄區查詢，來建立資料庫及表格，以及載入資料 blob 儲存體登錄區資料表。

    > [AZURE.NOTE] 如果資料是大，使用者可以建立磁碟分割區的登錄區資料表。 然後，使用者可以使用`for`迴圈中 Hadoop 上的命令列主節點將資料載入分割資料分割方式的登錄區資料表。

6.  探索資料，並視需要在 Hadoop 命令列中建立的功能。 請注意，不需要進行實體化資料庫資料表中的功能。 僅限注意建立所需的查詢。

    。  登入 Hadoop 叢集主節點

    b。  開啟 Hadoop 命令列。

    c。  以輸入登錄區根目錄`cd %hive_home%\bin`Hadoop 命令列中的商務連絡人。

    d。  探索資料，並視需要建立功能 Hadoop 叢集主節點上執行的登錄區查詢 Hadoop 命令列中的商務連絡人。

7.  如果有需要及/或想要範例資料以符合 Azure 電腦學習 Studio 中。

8.  [Azure 機器學習 Studio](https://studio.azureml.net/)登入。

9. 閱讀直接從資料`Hive Queries`使用[匯入資料][import-data]模組。 貼上所需的查詢會擷取欄位、 建立功能，並範例資料，如有需要直接在[匯入資料][import-data]查詢。

10. 簡單的 Azure 電腦學習嘗試開始上傳的資料集的流程。

## <a name="decisiontree"></a>針對案例選取範圍的決策樹
------------------------

下圖摘要列出上述案例的進階分析程序及所做的每個帶有案例讓您的技術選擇。 請注意，可能需要處理資料、 探索、 功能工程和取樣置於一或多個方法/環境-在來源中繼，及/或目標環境 –，可能會繼續反覆視。 圖表只能做為一些可能流量的圖例，並不會提供完整的列舉。

![範例 DS 程序逐步解說案例][8]

### <a name="advanced-analytics-in-action-examples"></a>動作範例中的 [進階分析

採用進階分析程序與技術使用公用資料集的端對端 Azure 電腦學習逐步解說，請參閱︰


* [小組資料科學處理程序] 動作︰ 使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)。
* [小組資料科學處理程序] 動作︰ 使用 HDInsight Hadoop 叢集](machine-learning-data-science-process-hive-walkthrough.md)。


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
