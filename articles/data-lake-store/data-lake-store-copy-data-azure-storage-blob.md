<properties
   pageTitle="將資料複製到資料湖存放 Azure 儲存體 Blob |Microsoft Azure"
   description="使用 AdlCopy 工具，將資料複製到資料湖存放區 Azure 儲存體二進位大型物件"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="copy-data-from-azure-storage-blobs-to-data-lake-store"></a>Azure 儲存體 Blob 將資料複製到資料湖存放區

> [AZURE.SELECTOR]
- [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

Azure 資料湖存放提供的命令列工具， [AdlCopy](http://aka.ms/downloadadlcopy)，將資料複製下列來源︰

* 從 Azure 儲存體 Blob 資料湖存放區。 您無法使用 AdlCopy，將資料複製到 Azure 儲存體 blob 資料湖存放區。

* 介於兩個 Azure 資料湖存放帳戶。 

此外，您可以使用 AdlCopy 工具在兩個不同的模式︰

* **獨立**的工具來執行的工作使用資料湖存放資源的位置。

* **使用資料湖分析帳戶**，指派給您的資料湖分析帳戶單位可用於執行複製作業。 您可能會想要使用這個選項，當您查看依預期方式執行複製工作。

##<a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

- **Azure 儲存體 Blob**容器含有一些資料。

- **Azure 資料湖分析帳戶 （可省略）** -請參閱[開始使用 Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)如需如何建立資料湖存放帳戶的指示。

- **AdlCopy 工具**。 從[http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy)安裝 AdlCopy 工具。

## <a name="syntax-of-the-adlcopy-tool"></a>AdlCopy 工具的語法

使用下列語法處理 AdlCopy 工具

    AdlCopy /Source <Blob or Data Lake Store source> /Dest <Data Lake Store destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Unit <Number of Analytics units> /Pattern 

以下是在語法參數︰

| 選項    | 描述                                                                                                                                                                                                                                                                                                                                                                                                          |
|-----------|------------|
| 來源    | Azure 儲存體 blob 中指定的來源資料的位置。 來源可以是 blob 容器、 blob，或是另一個資料湖存放帳戶。                                                                                                                                                                                                                                                                                                 |
| 目的地      | 指定複製到資料湖存放目的地。                                                                                                                                                                                                                                                                                                                                                                |
| SourceKey | 指定的儲存空間 Azure blob 來源的儲存空間便捷鍵。 只有當來源是 blob 容器或 blob，這是必要。                                                                                                                                                                                                                                                                                                                                                 |
| 帳戶   | **選用**。 如果您想要使用 Azure 資料湖分析帳戶執行複製工作，請使用此選項。 如果您使用 /Account 選項中的語法，但未指定的資料湖分析帳戶，AdlCopy 會使用預設帳戶執行工作。 此外，如果您使用此選項時，您必須新增來源 (Azure Blob 儲存體) 與目的地 （Azure 資料湖商店） 做為資料來源資料湖分析帳戶。  |
| 單位     |  指定資料湖分析單位用於複製工作的數目。 當您使用**/Account**選項來指定資料湖分析帳戶時，此選項。
| 圖樣   | 指定 regex 圖樣，指出哪一個二進位大型物件或要複製的檔案。 AdlCopy 使用區分大小寫相符。 在任何模式指定預設模式是複製所有項目。 不支援指定多個檔案模式。                                                                                                                                                                                                                                                                                                                                               



## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>從 Azure 儲存體 blob 複製資料使用 AdlCopy （為獨立版）

1. 開啟命令提示字元，並瀏覽至 AdlCopy 安裝的目錄，通常是`%HOMEPATH%\Documents\adlcopy`。

2. 執行下列命令以來源容器中的特定 blob 複製到資料湖存放區︰

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    例如︰

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    
    >[AZURE.NOTE] 上述語法指定的檔案複製到資料湖存放帳戶中的資料夾。 如果指定的資料夾名稱不存在，AdlCopy 工具會建立一個資料夾。

    會提示您輸入認證，您可以在其下有資料湖存放帳號的 Azure 訂閱。 您會看到類似以下的輸出︰

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. 您也可以複製一個容器中的所有 blob 資料湖存放帳戶使用下列命令︰

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    例如︰

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-store-account"></a>作為 AdlCopy （獨立），將資料複製到另一個資料湖存放區帳戶

您也可以使用 AdlCopy 兩個資料湖存放帳戶之間移動資料。

1. 開啟命令提示字元，並瀏覽至 AdlCopy 安裝的目錄，通常是`%HOMEPATH%\Documents\adlcopy`。

2. 執行下列命令以特定的檔案複製到另一個資料湖存放帳戶。

        AdlCopy /Source adl://<source_adls_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adls_account>.azuredatalakestore.net/<path>/

    例如︰

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

    >[AZURE.NOTE] 上述語法指定的檔案複製到目的地資料湖存放帳戶中的資料夾。 如果指定的資料夾名稱不存在，AdlCopy 工具會建立一個資料夾。

    會提示您輸入認證，您可以在其下有資料湖存放帳號的 Azure 訂閱。 您會看到類似以下的輸出︰

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

3. 下列命令會從來源資料湖存放帳戶中的特定資料夾中的所有檔案都複製到目的地資料湖存放帳戶中的資料夾。

        AdlCopy /Source adl://mydatastore.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestore.azuredatalakestore.net/mynewfolder/

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>使用 AdlCopy （資料湖分析帳戶） 來複製資料

您也可以使用您的資料湖分析帳戶執行，將資料複製到資料湖存放區的儲存空間 Azure blob AdlCopy 工作。 您通常會使用這個選項，gb 和 tb 的範圍中要移動的資料時，您想要更好與預期的效能處理量。

若要使用您的資料湖分析帳戶以複製 Azure 儲存體 Blob AdlCopy，必須作為資料來源的資料湖分析帳戶新增來源 (Azure Blob 儲存體)。 如需新增至您的資料湖分析帳戶的其他資料來源的相關指示，請參閱[管理資料湖分析帳戶資料來源](..//data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources)。

>[AZURE.NOTE] 如果您從 Azure 資料湖存放帳戶複製做為來源使用的是資料湖分析帳戶，您不需要建立的資料湖存放帳戶資料湖分析帳戶之間的關聯。 與資料湖分析帳戶建立關聯的來源存放區要求時，只來源是 Azure 儲存體帳戶。

執行下列命令以複製 Azure 儲存體 blob 資料湖存放帳戶使用資料湖分析帳戶︰

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

例如︰

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2


同樣地，執行下列命令以複製 Azure 儲存體 blob 資料湖存放帳戶使用資料湖分析帳戶︰

    AdlCopy /Source adl://mysourcedatalakestore.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastore.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>使用 AdlCopy 複製資料使用模式比對

您可以在此區段中，瞭解如何使用 AdlCopy，將資料複製為來源 （在以下範例我們使用 Azure 儲存體 Blob） 使用模式比對目的地資料湖存放帳戶。 例如，您可以使用下列步驟，從來源 blob 的所有副檔名.csv 檔案複製到目的地。

1. 開啟命令提示字元，並瀏覽至 AdlCopy 安裝的目錄，通常是`%HOMEPATH%\Documents\adlcopy`。

2. 執行下列命令以複製資料湖存放區特定 blob 從來源容器中的所有副檔名 *.csv 檔案︰

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    例如︰

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

    
## <a name="billing"></a>帳單

* 如果您是使用 AdlCopy 工具為獨立計費出口為的成本移動資料，如果來源 Azure 儲存體帳戶不是資料湖儲存為相同的區域。

* 如果您使用 AdlCopy 工具使用您的資料湖分析帳戶時，會套用標準[資料湖分析帳單費率](https://azure.microsoft.com/pricing/details/data-lake-analytics/)。

## <a name="considerations-for-using-adlcopy"></a>使用 AdlCopy 的考量

* （適用於版本 1.0.5） AdlCopy 支援複製資料的共同擁有超過數以千計的檔案和資料夾的來源。 不過，如果您遇到問題複製大型資料集，您可以將檔案或資料夾分散到不同的子資料夾並使用路徑這些子資料夾中做為來源改為。

## <a name="next-steps"></a>後續步驟

- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [使用資料湖存放 Azure 資料湖狀況分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [使用資料湖存放 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
