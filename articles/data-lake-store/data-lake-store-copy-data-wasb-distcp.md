<properties
   pageTitle="WASB 中往返複製資料到資料湖存放使用 Distcp |Microsoft Azure"
   description="使用 Distcp 工具來複製資料從 Azure 儲存體 Blob 資料湖存放區"
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
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>使用 Distcp Azure 儲存體二進位大型物件及資料湖存放區之間移動資料

> [AZURE.SELECTOR]
- [使用 DistCp](data-lake-store-copy-data-wasb-distcp.md)
- [使用 AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)


當您建立有權存取資料湖存放帳戶的 HDInsight 叢集之後時，您可以使用像 Distcp Hadoop 生態工具，將資料**與**HDInsight 叢集儲存空間 (WASB) 複製到資料湖存放帳戶。 本文提供如何達到這個目的指示。

##<a name="prerequisites"></a>必要條件

這份文件之前，您必須具備下列項目︰

- **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
- **啟用 Azure 訂閱**的公用資料湖存放區的預覽。 請參閱[相關指示](data-lake-store-get-started-portal.md#signup)。
- **Azure HDInsight 叢集**有權存取資料湖存放帳戶。 請參閱[建立資料湖存放 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)。 請確定您叢集啟用遠端桌面。

## <a name="do-you-learn-fast-with-videos"></a>您學到快速與影片嗎？

[觀看這段影片](https://mix.office.com/watch/1liuojvdx6sie)以 Azure 儲存體二進位大型物件及資料湖存放區之間移動資料的方式使用 DistCp。

## <a name="use-distcp-from-remote-desktop-windows-cluster-or-ssh-linux-cluster"></a>使用 Distcp 從遠端桌面 （Windows 叢集） 或 SSH （Linux 叢集）

HDInsight 叢集隨附 Distcp 公用程式，可以用來將來自不同來源的資料複製到 HDInsight 叢集。 如果您已設定 HDInsight 叢集使用資料湖存放為額外的儲存空間，Distcp 公用程式可以使用--現成的複製資料，以及資料湖存放帳戶。 此區段中，我們看看如何使用 Distcp 公用程式。

1. 如果您有 Windows 叢集，將 HDInsight 叢集遠端有存取權的資料湖存放帳戶。 如需相關指示，請參閱[連線至叢集使用 RDP](../hdinsight/hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)。 從桌面叢集開啟 Hadoop 命令列。

    如果您有 Linux 叢集，可用於 SSH 連線到叢集。 請參閱[連線至 Linux 型 HDInsight 叢集](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)。 從 SSH 提示執行命令。

3. 請確認您的電腦可以存取的 Azure 儲存體二進位大型物件 (WASB)。 執行下列命令︰

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    此應提供的清單中的儲存空間 blob 的目錄。

4. 同樣地，確認您是否可以從叢集來存取資料湖存放帳戶。 執行下列命令︰

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    此應提供資料湖存放帳戶中的檔案或資料夾的清單。

5. 使用 Distcp WASB 資料複製到資料湖儲存的帳戶。

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    在 [資料湖存放帳戶**/myfolder**這會****複製/範例/資料/約翰/資料夾的內容中 WASB。

6. 同樣地，使用 Distcp 資料湖存放帳戶將資料複製到 WASB。

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    這會在 [資料湖存放帳戶**/myfolder**的內容複製****到/範例/資料/約翰/WASB 中的資料夾。

## <a name="see-also"></a>另請參閱

- [Azure 儲存體 Blob 將資料複製到資料湖存放區](data-lake-store-copy-data-azure-storage-blob.md)
- [保護資料湖存放區中的資料](data-lake-store-secure-data.md)
- [使用資料湖存放 Azure 資料湖狀況分析](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [使用資料湖存放 Azure HDInsight](data-lake-store-hdinsight-hadoop-use-portal.md)
