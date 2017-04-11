<properties
    pageTitle="探索登錄區查詢的登錄區資料表中的資料 |Microsoft Azure"
    description="探索使用登錄區查詢的登錄區資料表中的資料。"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="bradsev" />

# <a name="explore-data-in-hive-tables-with-hive-queries"></a>探索登錄區查詢的登錄區資料表中的資料

這份文件提供登錄區指令碼範例，用來瀏覽 HDInsight Hadoop 叢集登錄區資料表中的資料。

主題說明如何使用工具來探索資料從各種不同的儲存空間環境下列**功能表**連結。

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## <a name="prerequisites"></a>必要條件
本文假設您有︰

* 建立 Azure 儲存體帳戶。 如果您需要的指示，請參閱[建立 Azure 儲存體帳戶](../storage/storage-create-storage-account.md#create-a-storage-account)
* 佈建自訂的 Hadoop 叢集 HDInsight 服務。 如果您需要的指示，請參閱[進階分析自訂 Azure HDInsight Hadoop 叢集](machine-learning-data-science-customize-hadoop-cluster.md)。
* 資料已上傳至 Azure HDInsight Hadoop 叢集登錄區資料表。 如果沒有出現，請遵循[建立和載入資料登錄區資料表](machine-learning-data-science-move-hive-tables.md)中的指示，先將資料上傳至登錄區資料表。
* 啟用遠端存取叢集。 如果您需要的指示，請參閱[存取 Hadoop 叢集不對節點](machine-learning-data-science-customize-hadoop-cluster.md#headnode)。
* 如果您需要如何送出登錄區查詢的相關指示，請參閱[如何送出登錄區查詢](machine-learning-data-science-move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>範例的資料瀏覽登錄區查詢指令碼

1. 取得觀察每個資料分割的計數 `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. 取得觀察每日的計數 `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. 取得分類的資料行中的階層數  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. 在兩個類別的資料行的組合取得階層的數 `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. 取得的分配的數值的資料行  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. 聯結兩個資料表中擷取記錄

        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>其他查詢計程車差旅費資料案例的指令碼

範例查詢的特定[NYC 計程車差旅費資料](http://chriswhong.com/open-data/foil_nyc_taxi/)案例也會提供在[Github 存放庫](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)。 這些查詢已經有指定的資料結構描述，並準備好提交執行。
