<properties
    pageTitle="小組資料 Science 程序中的動作： 使用 SQL Server |Microsoft Azure"
    description="進階的分析程序與巨集指令中的技術"  
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
    ms.author="fashah;bradsev"/>


# <a name="the-team-data-science-process-in-action-using-sql-server"></a>在巨集指令在小組資料 Science 程序： 使用 SQL Server

在此教學課程、 您逐步解說中建立及部署使用 SQL Server 與公開可用的資料集- [NYC 滑行行程](http://www.andresmh.com/nyctaxitrips/)資料集的機器學習模型。 此程序遵循標準資料 science 工作流程： 多快擷取了解及探索資料，以促進學習，然後建置及部署模型的工程師功能。


## <a name="dataset"></a>NYC 滑行行程 Dataset 描述

NYC 滑行來回資料約 20 GB 的壓縮 CSV 檔 (未壓縮 ~ 48 GB)，包含多個 173 萬個個別行程與 fares 付費的每個來回。 來回的每筆記錄包含收取] 目錄和暫時擱置的位置和時間、 匿名可回復 (driver) 照編號和 medallion （滑行的唯一識別碼） 數字。 資料在 2013 年涵蓋所有的行程，並提供下列兩個集中的每個月：

1. 'trip_data' CSV 包含來回的詳細資訊，例如乘客、 收取] 目錄和 dropoff 點、 來回時間及來回長度的數目。 以下是一些範例記錄：

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip_fare' CSV 包含的每個來回，例如付款類型、 fare 量、 附加費和含稅額、 秘訣及費用、 付費 fare 和總量付費的詳細資料。 以下是一些範例記錄：

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

要加入來回的唯一索引鍵\_資料和來回\_fare 由欄位所組成： medallion 具 「 可回復\_授權和收取] 目錄\_datetime。

## <a name="mltasks"></a>預測工作範例

我們將制訂根據三個預測問題*提示\_量*、 nm-oc-13-1st：

1. 二進位分類︰ 預測是否提示所支付的來回亦*提示\_量*那是更大超過 $0 為正數的範例，時*提示\_量*$0 是負數的範例。

2. Multiclass 分類︰ 預測的提示的來回付費範圍。 我們劃分為*提示\_量*五個筒或類別：

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. 迴歸任務︰ 預測的提示的來回付費量。  


## <a name="setup"></a>Azure 向上設定資料 science 環境的進階的分析

如您[規劃您的環境](machine-learning-data-science-plan-your-environment.md)指南所見，有一些選項以使用 Azure 中的 NYC 滑行行程資料集：

- 使用 Azure blob 中的資料則模型中的 Azure 機器學習
- 將資料載入到 SQL Server 資料庫則模型中的 Azure 機器學習

在本教學課程中我們將示範平行大量匯入至 SQL Server 資料探索、 功能資料 engineering 和 down 取樣使用 SQL Server Management Studio，以及使用 IPython 筆記本。 在 GitHub 中共用[範例指令碼](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)及[IPython 筆記本](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)。 使用 Azure blob 中的資料範例 IPython 筆記本也有可在相同位置。

設定 「 Azure 資料 Science 環境：

1. [建立儲存帳戶](../storage/storage-create-storage-account.md)

2. [建立 Azure 機器學習工作區](machine-learning-create-workspace.md)

3. [佈建資料 Science 虛擬機器](machine-learning-data-science-setup-sql-server-virtual-machine.md)，其中會做為 SQL Server IPython 筆記本 server。

    > [AZURE.NOTE] 範例指令碼及 IPython 筆記本將會下載到您資料 Science 的虛擬機器在設定過程中。 VM 後續安裝指令碼完成時，範例會在虛擬機器的文件庫：  
    > - 範例指令碼：`C:\Users\<user_name>\Documents\Data Science Scripts`  
    > - 範例 IPython 筆記本：`C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
    > 其中`<user_name>`是虛擬機器的 Windows 登入名稱。 我們將為**範例指令碼**與**範例 IPython 筆記本**的範例資料夾參照。


根據資料集大小、 資料來源位置，與所選的 Azure 目標環境，這種情況很類似[案例\#5： 在本機檔案中的大型資料集目標 Azure 虛擬機器中的 SQL Server](../machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb)。

## <a name="getdata"></a>從公用的來源取得資料

若要從其公用位置取得[NYC 滑行行程](http://www.andresmh.com/nyctaxitrips/)資料集，您可以使用任何[移動資料 Azure Blob 存放區](machine-learning-data-science-move-azure-blob.md)中所述方法將資料複製到新的虛擬機器。

複製使用 AzCopy 資料：

1. 登入您的虛擬機器 (VM)

2. 在虛擬機器的資料磁碟中建立新目錄 (請注意： 不使用其隨附的資料磁碟作為 VM 暫存磁碟)。

3. 在命令提示字元視窗中，執行下列 Azcopy 命令列中 < path_to_data_folder > 取代 (2) 中建立您的資料資料夾：

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

    總共 24 個 AzCopy 完成時，壓縮 CSV 檔案 (來回 12\_資料和來回 12\_fare) 應位於 [資料] 資料夾中。

4. 解壓縮下載的檔案。 記下解壓縮的檔案所在的資料夾。 若要為參照此資料夾 < 路徑\_至\_資料\_檔案\>。

## <a name="dbload"></a>將 SQL Server 資料庫的大量匯入資料

載入/傳送大量資料的 SQL 資料庫及後續的查詢效能可改善使用_分割表格與檢視_。 在本節，我們將遵循三[平行大量資料匯入使用 SQL 磁碟分割表格](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)來建立新的資料庫並將資料載入平行的已分割表格中所述的指示。

1. 雖然登入您的虛擬機器，啟動**SQL Server Management Studio**。

2. 使用 [Windows 驗證連線。

    ![SSMS 連線][12]

3. 如果您有尚未變更 SQL Server 驗證模式，建立新的 SQL 登入使用者開啟名為指令碼檔案**變更\_auth.sql** **範例指令碼**] 資料夾中。 變更預設的使用者名稱和密碼。 按一下 [ **！執行**執行指令碼工具列中。

    ![執行指令碼][13]

4. 確認和/或變更的 SQL Server 預設資料庫和記錄檔資料夾以確保新建立的資料庫會儲存在資料磁碟。 SQL Server 虛擬機器映像的 datawarehousing 負載最佳化是預先設定資料和記錄檔磁碟。 如果您的虛擬機器不包含資料磁碟與您在虛擬機器設定過程中加入新的虛擬硬碟，變更預設資料夾，如下所示：

    - 以滑鼠右鍵按一下 [左] 面板中的 SQL Server 名稱並按一下 [**內容**]。

        ![SQL Server 屬性][14]

    - 從 [**選取頁面]**清單中選取 [**資料庫設定**左方。

    - 驗證及 （或) 變更為您選擇的**資料磁碟**位置的**資料庫預設位置**。 這是如果建立的預設位置設定新的資料庫所在的位置。

        ![SQL 資料庫的預設值][15]  

5. 若要建立新的資料庫以及一組要保留已分割的表格將檔案群組，請開啟 [指令碼範例**建立\_db\_default.sql**。 指令碼會建立名為**TaxiNYC**和 12 檔案群組中的預設資料位置的新資料庫。 每個檔案群組將會保留 1 個月的來回\_資料和來回\_最好資料。 如果想要，修改資料庫名稱。 按一下 [ **！執行**執行指令碼。

6. 接下來，建立兩個磁碟分割表格，其中的來回\_資料與另一個用於來回\_fare。 開啟範例指令碼**建立\_分割\_table.sql**，這會：

    - 建立每月分割資料分割函式。
    - 建立對應至不同的檔案群組的每個月資料分割配置。
    - 建立對應的分割區配置到兩個分割的表格： **nyctaxi\_來回**保存來回\_資料和**nyctaxi\_fare**保存來回\_最好的資料。

    按一下 [ **！執行**執行指令碼並建立已分割的資料表。

7. 在 [**範例指令碼**] 資料夾中，有兩個 PowerShell 範例指令碼將會示範如何平行大量匯入的 SQL Server 表格的資料。

    - **bcp\_平行\_generic.ps1**是一般指令碼以平行的大量匯入資料插入資料表。 修改此指令碼以設定輸入與目標變數指令碼中的註解行所示。
    - **bcp\_平行\_nyctaxi.ps1**是預先設定的一般的指令碼的版本及可用以載入 NYC 滑行行程資料的兩個資料表。  

8. 右鍵**bcp\_平行\_nyctaxi.ps1**指令碼名稱，然後按一下 [**編輯**] 來開啟 PowerShell 中。 檢閱預設的變數並根據您所選取的資料庫名稱、 輸入的 data] 資料夾、 目標記錄資料夾與路徑範例格式的檔案**nyctaxi_trip.xml**修改和**nyctaxi\_fare.xml** （提供**範例指令碼**] 資料夾中）。

    ![大量匯入資料][16]

    您也可以選取的驗證模式，預設值為 [Windows 驗證。 按一下 [執行] 工具列中的綠色箭頭。 指令碼會啟動 24 大量匯入作業平行 12 中的每個已分割的資料表。 您可以監視資料匯入進度上述設定開啟 [SQL Server 預設 data] 資料夾。

9. PowerShell 指令碼回報起始和結束時間集。 當所有大量匯入完成時，會報告的結束時間。 檢查以確認大量匯入成功，亦即目標記錄資料夾中的任何錯誤報告的目標記錄資料夾。

10. 您的資料庫現在是備妥可供探索、 功能工程和想要為其他作業。 自資料表根據至分割**收取] 目錄\_datetime**欄位，其中包含查詢**收取] 目錄\_datetime** **WHERE**子句中的條件有益的分割區配置。

11. 在**SQL Server Management Studio**中，探索所提供的範例指令碼**範例\_queries.sql**。 若要執行任何查詢範例，請反白顯示查詢行然後按一下 [ **！執行**工具列中。

12. NYC 滑行行程資料載入兩個不同資料表中。 若要改善聯結作業，強烈建議編製索引的資料表。 範例指令碼**建立\_分割\_index.sql**複合式加入機碼上建立已分割的索引**medallion 具 「 可回復\_授權，並收取] 目錄\_datetime**。

## <a name="dbexplore"></a>資料探索及 SQL Server 中的功能工程

在本節，我們會直接在使用稍早所建立的 SQL Server 資料庫的**SQL Server Management Studio**中執行 SQL 查詢執行資料探索及功能產生。 範例指令碼名為**範例\_queries.sql**提供**範例指令碼**] 資料夾中。 如果與預設的不同修改要變更資料庫名稱，指令碼： **TaxiNYC**。

在此練習，我們將會：

- 連線至**SQL Server Management Studio**中使用 Windows 驗證或使用 SQL 驗證的 SQL 登入名稱與密碼。
- 探索資料散發的不同時間 windows 中的一些欄位。
- 調查資料品質緯度和經度欄位。
- 產生二進位及 multiclass 分類標籤根據**提示\_量**。
- 產生功能及 compute 比較來回距離。
- 聯結兩個表格並解壓縮的隨機的範例會將用來建立模型。

當您準備好繼續進行至 Azure 機器學習時，您可以：  

1. 儲存最終 SQL 查詢來擷取和範例資料及複製-貼上查詢直接將[匯入資料][import-data]中 Azure 機器學習、 模組或
2. 持續性您想要用於建立新的資料庫資料表中的模型並使用新表格中[匯入資料]的取樣及工程資料[import-data]中 Azure 機器學習模組。

本章節中我們將儲存最終的查詢來擷取和範例資料。 第二個方法會示範在[資料探索及功能工程 IPython 筆記本中的區域](#ipnb)] 區段中。

為快速驗證中填入稍早使用平行大量匯入的表格列和欄數

    -- Report number of rows in table nyctaxi_trip without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')

    -- Report number of columns in table nyctaxi_trip
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'

#### <a name="exploration-trip-distribution-by-medallion"></a>依 medallion 探索： 來回分配

本範例會在指定的期間內識別與 100 個以上的行程的 medallion （滑行號碼）。 查詢會受益分割的表格存取由於它由的分割區配置容許**收取] 目錄\_datetime**。 查詢完全 dataset 將也要使用的已分割的資料表及/或索引掃描。

    SELECT medallion, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

#### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Medallion 及 hack_license 所探索︰ 來回通訊群組

    SELECT medallion, hack_license, COUNT(*)
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>資料品質評估： 確認記錄中具有正確的緯度和/或經度

此範例會檢查所有緯度和/或經度欄位也包含無效的值 （弧度度應該-90 到 90 之間），或有 （0，0） 座標。

    SELECT COUNT(*) FROM nyctaxi_trip
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>探索︰ Tipped 相對於不傾斜行程通訊群組

此範例會尋找已傾斜相對於不傾斜中指定的時間期間 （或完整的資料集如果涵蓋完整的年份中） 的行程數目。 此通訊群組會反映稍後要用於二進位分類模型 （英文） 二進位標籤分配。

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM nyctaxi_fare
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

#### <a name="exploration-tip-classrange-distribution"></a>探索︰ 提示類別/範圍通訊群組

本範例會計算已指定的期間 （或完整的資料集如果涵蓋完整的年份） 提示範圍的通訊。 這是將用於 multiclass 分類模型 （英文） 的更新版本的標籤類別的散佈。

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_fare
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

#### <a name="exploration-compute-and-compare-trip-distance"></a>探索︰ 計算和比較來回距離

此範例會將轉換收取] 目錄和暫時擱置緯度和經度 SQL 地理位置 points、 計算來回距離使用 SQL geography 點差異，並傳回隨機範例進行比較的結果。 範例結果限制為僅使用稍早所涵蓋的資料品質評估查詢的有效座標。

    SELECT
    pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
    ,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
    ,trip_distance
    ,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
    FROM nyctaxi_trip
    tablesample(0.01 percent)
    WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### <a name="feature-engineering-in-sql-queries"></a>在 SQL 查詢中的功能工程

標籤轉換探索產生與 geography 查詢也可用來產生標籤/功能移除計數組件。 其他功能工程 SQL 範例提供的[資料探索及功能工程 IPython 筆記本中的區域](#ipnb)] 區段中。 它是完整的資料集或使用直接在 SQL Server 資料庫執行個體執行的 SQL 查詢的大型子集上執行的功能產生查詢更有效率。 查詢可能會執行**SQL Server Management Studio**、 IPython 筆記本或任何開發工具/環境可以在本機或遠端存取資料庫中。

#### <a name="preparing-data-for-model-building"></a>準備資料模型建立的

下列查詢聯結**nyctaxi\_來回**和**nyctaxi\_fare**資料表、 二進位分類標籤**傾斜**、 多重類別分類標籤將會產生**提示\_類別**，以及從完整聯結的資料集擷取 1%隨機樣本。 此查詢可以複製然後直接在[Azure 機器學習 Studio](https://studio.azureml.net) [匯入資料]貼上[import-data]直接資料擷取從 Azure 中的 SQL Server 資料庫執行個體的模組。 查詢排除含有不正確的記錄 （0，0） 座標。

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 percent)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>資料探索及 IPython 筆記本中的功能工程

在本節，我們將會執行資料探索及功能產生使用針對稍早所建立的 SQL Server 資料庫的 Python 與 SQL 查詢。 名為**machine-Learning-data-science-process-sql-story.ipynb**範例 IPython 筆記本提供**範例 IPython 筆記本**資料夾中。 此筆記本也是可在[GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)。

以下是建議的順序大資料搭配使用時︰

- 在小型資料的範例讀入記憶體內的資料圖文框。
- 執行一些視覺效果和 explorations 使用取樣的資料。
- 實驗功能工程使用取樣的資料。
- 針對較大型資料探索、 資料操作及功能工程、 使用 Python 發出直接對在 Azure 虛擬機器中的 SQL Server 資料庫的 SQL 查詢。
- 決定要使用 Azure 機器學習模型建置樣本大小。

準備進行 Azure 機器學習，當您可以：  

1. 儲存最終 SQL 查詢解壓縮和範例資料並複製-貼上查詢將[匯入資料]直接[import-data]中 Azure 機器學習模組。 此方法會示範在[Azure 機器學習建置模型](#mlmodel)] 區段中。    
2. 保存您計劃要用於建立新的資料庫資料表中的模型取樣和工程資料，請在 [[匯入資料]使用新表格[import-data]模組。

以下是一些資料探索、 資料視覺效果和 engineering 範例的功能。 更多範例，請參閱範例 SQL IPython 筆記本**範例 IPython 筆記本**資料夾中。

#### <a name="initialize-database-credentials"></a>初始化資料庫認證

初始化您下列變數中的資料庫連線設定：

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### <a name="create-database-connection"></a>建立資料庫的連線

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>報表數目的列與欄的表格 nyctaxi_trip

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('nyctaxi_trip')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('nyctaxi_trip')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- 總列數 = 173179759  
- 總欄數 = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>讀取中的 SQL Server 資料庫的小型資料範例

    t0 = time.time()

    query = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (0.05 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

閱讀範例表格是 6.492000 秒的時間  
擷取的列和欄數 = （84952、 21）

#### <a name="descriptive-statistics"></a>描述性的統計資料

現在就可以探索取樣的資料。 我們一開始是以查看描述性統計資料**來回\_距離**（或任何其他） 欄位：

    df1['trip_distance'].describe()

#### <a name="visualization-box-plot-example"></a>視覺效果:] 方塊中繪圖範例

接下來我們查看來回距離視覺化 quantiles 方塊繪圖

    df1.boxplot(column='trip_distance',return_type='dict')

![繪製 #1][1]

#### <a name="visualization-distribution-plot-example"></a>視覺效果： 分配繪圖範例

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![繪製 #2][2]

#### <a name="visualization-bar-and-line-plots"></a>視覺效果︰ 列和線條繪圖

在這個範例中，我們 bin 來回距離將五個 bins 和視覺化 binning 結果。

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

我們可以繪製上述紙匣通訊在列中或線條繪圖作為下

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![繪製 #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![繪製 #4][4]

#### <a name="visualization-scatterplot-example"></a>視覺效果︰ Scatterplot 範例

我們將示範之間的散佈圖繪圖**來回\_時間\_中\_秒鐘**和**來回\_距離**以查看是否有任何相互關聯

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![繪製 #6][6]

同樣地我們可以檢查之間的關聯性**率\_程式碼**和**來回\_距離**。

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![繪製 #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>子取樣 SQL 中的資料

做準備的資料模型建立[Azure 機器學習 Studio](https://studio.azureml.net)中，您可能決定**要直接在 [匯入資料] 模組中使用的 SQL 查詢**或保存在新的表格，您可以使用在 [[匯入資料]的工程和取樣資料[import-data]與簡單單元**選取*FROM < 您\_新\_表格\_名稱 >**。

本章節中我們將建立用以保留取樣和工程資料的新資料表。 [資料探索及功能 Engineering 在 SQL Server](#dbexplore) ] 區段中提供的模型建置直接 SQL 查詢範例。

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>建立範例資料表並填入 1%的已聯結資料表。 拖第一個表格中存在。

在本節，我們加入資料表**nyctaxi\_來回**和**nyctaxi\_fare**、 解壓縮 1%隨機範例中，並保存在新的資料表名稱取樣的資料**nyctaxi\_一個\_%**：

    cursor = conn.cursor()

    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''

    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
        INTO nyctaxi_one_percent
        FROM nyctaxi_trip t, nyctaxi_fare f
        TABLESAMPLE (1 PERCENT)
        WHERE t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
        AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''

    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>使用 SQL 查詢 IPython 筆記本中的資料探索

在本節，我們探索資料散發使用上述我們建立的新資料表中保存 1%取樣的資料。 請注意可以使用原始表格 （選用） 使用**TABLESAMPLE**來限制探索範例或限制結果，以指定的時間期間使用執行類似 explorations**收取] 目錄\_datetime**分割[資料探索與 SQL Server 中工程功能](#dbexplore)] 區段中所示。

#### <a name="exploration-daily-distribution-of-trips"></a>探索： 每日通訊群組的行程

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>每個 medallion 探索︰ 來回通訊群組

    query = '''
        SELECT medallion,count(*) AS c
        FROM nyctaxi_one_percent
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>使用 SQL 查詢 IPython 筆記本中的功能產生

本節中我們將會產生新標籤並直接使用 SQL 查詢的功能、 作業系統上 1%範例資料表後我們建立前一節。

#### <a name="label-generation-generate-class-labels"></a>標籤產生： 產生類別標籤

在下列範例中，我們會產生兩組用於模型 （英文） 的標籤：

1. 二進位類別標籤**傾斜**（預測是否將獲得提示）
2. Multiclass 標籤**tip\_類別**（預測 tip 紙匣或範圍）

        nyctaxi_one_percent_add_col = '''
            ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
        '''

        cursor.execute(nyctaxi_one_percent_add_col)
        cursor.commit()

        nyctaxi_one_percent_update_col = '''
            UPDATE nyctaxi_one_percent
            SET
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

        cursor.execute(nyctaxi_one_percent_update_col)
        cursor.commit()

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>類別資料行的功能 Engineering： Count 功能

本範例會來取代其資料中的項目計數中的每個類別中的數字欄位轉換分類] 欄位。

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B AS
        (
            SELECT medallion, hack_license,
                SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
                SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
            FROM nyctaxi_one_percent
            GROUP BY medallion, hack_license
        )

        UPDATE nyctaxi_one_percent
        SET nyctaxi_one_percent.cmt_count = B.cmt_count,
            nyctaxi_one_percent.vts_count = B.vts_count
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion AND A.hack_license = B.hack_license
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>數值資料行的功能工程︰ 紙匣功能

本範例會轉換成預設的類別範圍，亦即轉換數字] 欄位的分類欄位中連續的數字] 欄位。

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
        (
            SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
            NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
        )

        UPDATE nyctaxi_one_percent
        SET trip_time_bin = B.BinNumber
        FROM nyctaxi_one_percent A INNER JOIN B
        ON A.medallion = B.medallion
        AND A.hack_license = B.hack_license
        AND A.pickup_datetime = B.pickup_datetime
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>功能工程︰ 解壓縮小數點的緯度經度位置功能

本範例會中斷緯度和/或經度欄位的十進位表示關閉到多個地區欄位的不同的資料粒度，如國家/地區、 縣/市、 鄉鎮、 封鎖等。記下新的地理位置欄位所無法對應到實際的位置。 如需對應 geocode 位置資訊，請參閱[Bing 地圖服務 REST 服務](https://msdn.microsoft.com/library/ff701710.aspx)。

    nyctaxi_one_percent_insert_col = '''
        ALTER TABLE nyctaxi_one_percent
        ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
            l5 varchar(3), l6 varchar(3), l7 varchar(3)
    '''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET l1=round(pickup_longitude,0)
            , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
            , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
            , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
            , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
            , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
            , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
    '''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### <a name="verify-the-final-form-of-the-featurized-table"></a>驗證 featurized 表格的最後一個表單

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

我們已備妥可繼續模型建置和模型部署在[Azure 機器學習](https://studio.azureml.net)。 此資料是備妥可供任何先前，識別 nm-oc-13-1st 預測問題：

1. 二進位分類︰ 預測是否提示所支付的來回。

2. Multiclass 分類︰ 預測提示付費，根據先前所定義的類別的範圍。

3. 迴歸任務︰ 預測的提示的來回付費量。  


## <a name="mlmodel"></a>在 Azure 機器學習建置模型

若要開始建立模型練習，登入與 Azure 機器學習工作區。 如果尚未建立學習 workspace 機器，請參閱 ＜ [Create Azure 機器學習工作區](machine-learning-create-workspace.md)。

1. 若要開始使用 Azure 機器學習，請參閱[什麼是 Azure 機器學習 Studio？](machine-learning-what-is-ml-studio.md)

2. [Azure 機器學習 Studio](https://studio.azureml.net)登入。

3. Studio 首頁提供豐富資訊、 影片、 教學課程、 連結的模組參考 （英文）、 及其他資源。 檔 Azure 機器學習的詳細資訊，請參閱[Azure 機器學習文件中心](https://azure.microsoft.com/documentation/services/machine-learning/)。

典型的訓練實驗是由下列項目所組成：

1. 建立**+ 新增**實驗。
2. 取得 Azure 機器學習的資料。
3. 預先處理、 轉換和操作所需的資料。
4. 視產生功能。
5. 分割成訓練/驗證/測試資料集的資料 (或是有不同的資料集的每一個)。
6. 選取一個以上機器學習演算法根據解決學習問題。 例如二進位分類、 multiclass 分類、 迴歸。
7. 訓練使用訓練資料集的一或多個模型。
8. 分數使用受過訓練的模型的驗證資料集。
9. 評估要計算學習問題的相關評量模型。
10. 可正常調整模型，並選取要部署的最佳模型。

在此練習，我們有已探索工程 SQL Server 中的資料與多快擷取在 Azure 機器學習決定在樣本大小。 建立一個以上的預測模型我們決定：

1. 取得 Azure 機器學習使用[匯入資料]的資料[import-data]模組，可用的**資料輸入及輸出**] 區段中。 如需詳細資訊，請參閱 ＜[匯入資料][import-data]模組參考 （英文） 頁面。

    ![Azure 機器學習匯入資料][17]

2. 選取**Azure SQL 資料庫**做為 [**內容**] 面板中的**資料來源**。

3. 在 [**資料庫伺服器名稱**] 欄位中輸入資料庫 DNS 名稱。 格式：`tcp:<your_virtual_machine_DNS_name>,1433`

4. 在對應的欄位中輸入**資料庫名稱**。

5. 輸入**SQL 使用者名稱**格式**伺服器使用者 aqccount 名稱及密碼中的**Server 使用者帳戶密碼 * *。

6. 檢查**接受任何伺服器憑證**] 選項。

7. 在 [**資料庫查詢**編輯文字] 區域中，貼上查詢以擷取必要資料庫 （包含任何計算的欄位標籤等） 的欄位及向下範例資料所需的樣本大小。

二進位分類項實驗中直接從 SQL Server 資料庫讀取資料的範例會在下圖。 類似實驗可以建構 multiclass 分類與迴歸問題。

![Azure 機器 Learning 訓練][10]

> [AZURE.IMPORTANT] 在模型 （英文） 資料擷取和取樣查詢範例提供在前面的小節，**所有三個模型 （英文） 練習標籤已包含在查詢**中。 每一個模型 （英文） 練習重要 （必要） 步驟是要**排除**的其他兩個的問題，以及任何其他**目標外洩**不必要的標籤。 若是例如時使用的二進位分類，可使用**傾斜**標籤並排除欄位**tip\_類別**、 **tip\_量**、 和**總\_量**。 後者是目標遺漏，由於他們暗示提示付費。
>
> 排除不必要的欄和/或目標遺漏，您可以使用[選取欄中的資料集][select-columns]模組或[編輯中繼資料][edit-metadata]。 如需詳細資訊，請參閱[選取欄中的資料集][select-columns]及[編輯中繼資料][edit-metadata]參考頁面。

## <a name="mldeploy"></a>部署在 Azure 機器學習模型

當您模型準備就緒時，可以輕鬆地將其部署為直接從實驗的 web 服務。 如需部署 Azure 機器學習 web 服務的詳細資訊，請參閱[部署 Azure 機器學習 web 服務](machine-learning-publish-a-machine-learning-web-service.md)。

若要部署新的 web 服務，您需要：

1. 建立計分符合原先的假設。
2. 部署 web 服務。

從**已經完成**訓練實驗建立計分項實驗中，按一下 [**建立計分實驗**較低的巨集指令列中。

![Azure 計分][18]

Azure 機器學習嘗試建立計分項實驗中依據訓練實驗的元件。 特別是，它將會：

1. 儲存受過訓練的模型及模型訓練單元中移除。
2. 識別要表示預期的輸入的資料的結構描述邏輯**輸入連接埠**。
3. 識別要表示預期的 web 服務的輸出結構描述邏輯**輸出連接埠**。

建立計分實驗之後，請檢閱其並視需要調整。 一般調整是要取代輸入的資料集和/或查詢一個以排除標籤欄位為這些將無法使用時呼叫此服務。 它也是很好的作法以輸入的資料集和/或查詢的大小降至幾個記錄足夠只是表示將輸入的結構描述。 輸出連接埠會排除所有輸入的欄位及僅使用[選取的欄中的資料集]在輸出中包含**計分標籤**和**計分機率**[select-columns]模組。

下圖是計分實驗的範例。 準備部署，按一下 [在較低的巨集指令列中的 [**發佈 WEB 服務**] 按鈕。

![Azure 機器學習發佈][11]

若要重溫，這個逐步教學課程中您已建立的 Azure 資料 science 環境，以從資料擷取至模型訓練及部署 Azure 機器學習 web 服務的大型公用資料集正常運作。

### <a name="license-information"></a>授權資訊。

此範例逐步解說和其伴隨指令碼及 IPython notebook(s) 共用 microsoft MIT 授權。 請檢查 LICENSE.txt 檔案上 GitHub 如需詳細資訊的範例程式碼的目錄中。

### <a name="references"></a>參考資料

• [Andrés Monroy NYC 滑行行程下載頁面](http://www.andresmh.com/nyctaxitrips/)  
•[阻撓 Chris Whong NYC 的滑行來回資料](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC 滑行 Limousine 美國證管會參考資料與統計資料](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
