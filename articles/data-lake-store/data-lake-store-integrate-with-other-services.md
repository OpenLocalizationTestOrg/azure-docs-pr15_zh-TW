<properties
   pageTitle="與其他 Azure 服務整合資料湖存放 |Microsoft Azure"
   description="了解資料湖存放如何與其他 Azure 服務整合"
   documentationCenter=""
   services="data-lake-store"
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

# <a name="integrating-data-lake-store-with-other-azure-services"></a>與其他 Azure 服務整合資料湖存放區

Azure 資料湖存放可用與其他 Azure 服務搭配使用來啟用較大範圍的案例。 下列文章列出資料湖存放可以與整合服務。

## <a name="use-data-lake-store-with-azure-hdinsight"></a>使用資料湖存放區 Azure HDInsight

您可以提供使用資料湖存放為 HDFS 相容的儲存空間[Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)叢集。 這個版本中，為 Hadoop 和大量叢集在視窗和 Linux，您可以使用資料湖存放只為額外的儲存空間。 這類叢集仍會使用 Azure 儲存空間 (WASB) 為預設儲存空間。 不過，在視窗和 Linux HBase 叢集，您可以使用資料湖存放作為預設儲存空間，或額外的儲存空間，或兩者。

如需如何佈建資料湖存放 HDInsight 叢集上的指示，請參閱︰

* [使用資料湖存放區使用 Azure 入口網站提供 HDInsight 叢集](data-lake-store-hdinsight-hadoop-use-portal.md)
* [佈建 HDInsight 叢集，使用 PowerShell 的 Azure 資料湖存放區](data-lake-store-hdinsight-hadoop-use-powershell.md)

**比較偏好視訊嗎？** 請遵循下列連結，觀看影片說明如何使用 HDInsight 叢集資料湖存放區。

* [建立 HDInsight 叢集有權存取資料湖存放區](https://mix.office.com/watch/l93xri2yhtp2)
* 設定完畢之後叢集，[使用登錄區及豬指令碼的資料湖存放區中的 Access 資料](https://mix.office.com/watch/1n9g5w0fiqv1q)


## <a name="use-data-lake-store-with-azure-data-lake-analytics"></a>使用 Azure 資料湖分析與資料湖存放區

[Azure 資料湖分析](../data-lake-analytics/data-lake-analytics-overview.md)，可讓您使用在雲端的大型資料。 動態佈建新的資源，並可讓您執行 tb 或甚至 exabyte 可以儲存在支援的資料來源，其中的資料湖存放數字的資料分析。 使用 Azure 資料湖存放-提供最高層級的效能與處理量，為您的平行大型資料負載特別最佳化資料湖分析。

如需如何使用資料湖分析與資料湖存放區上的指示，請參閱[使用資料湖存放資料湖分析快速入門](../data-lake-analytics/data-lake-analytics-get-started-portal.md)。

**比較偏好視訊嗎？** 請遵循下列連結，觀看影片說明如何使用 HDInsight 叢集資料湖存放區。

* [Azure 資料湖分析連線至 Azure 資料湖存放區](https://mix.office.com/watch/qwji0dc9rx9k)
* [Access 資料湖分析透過 Azure 資料湖存放](https://mix.office.com/watch/1n0s45up381a8)


## <a name="use-data-lake-store-with-azure-data-factory"></a>使用資料湖存放區 Azure 資料工廠

您可以使用[Azure 資料工廠](https://azure.microsoft.com/services/data-factory/)來內嵌 Azure 資料表、 Azure SQL 資料庫、 Azure SQL 資料倉儲、 Azure 儲存體二進位大型物件和內部部署資料庫中的資料。 正在公民 Azure 生態中的，Azure 資料工廠可以用來協調 Azure 資料湖存放這些來源資料的 ingestion。

如需如何使用 Azure 資料工廠資料湖存放區的相關指示，請參閱[移動資料與使用資料工廠資料湖存放區](../data-factory/data-factory-azure-datalake-connector.md)。

**一次影片 ！** 請參閱[使用 Azure 資料工廠 Azure 資料湖市集資料協調流程](https://mix.office.com/watch/1oa7le7t2u4ka)。 

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-store"></a>將資料複製到資料湖存放 Azure 儲存體二進位大型物件

Azure 資料湖市集提供的命令列工具 AdlCopy，可讓您從 Azure Blob 儲存體中複製資料至資料湖存放區帳戶。 如需詳細資訊，請參閱[複製資料湖存放區 Azure 儲存體 Blob 的資料](data-lake-store-copy-data-azure-storage-blob.md)。

## <a name="copy-data-between-azure-sql-database-and-data-lake-store"></a>Azure SQL 資料庫及資料湖存放區之間移動資料

您可以使用 Apache Sqoop 匯入及匯出資料湖存放 Azure SQL 資料庫之間的資料。 如需詳細資訊，請參閱[複製資料湖儲存和使用 Sqoop Azure SQL 資料庫之間的資料](data-lake-store-data-transfer-sql-sqoop.md)。

**觀看這段影片**上[使用 Apache Sqoop 移動關聯的來源和 Azure 資料湖存放區之間的資料](https://mix.office.com/watch/1butcdjxmu114)。

## <a name="use-data-lake-store-with-stream-analytics"></a>使用資料流分析與資料湖存放區

您可以使用做為其中一個輸出的資料湖存放區來儲存資料串流使用 Azure 資料流分析。 如需詳細資訊，請參閱[從 Azure 資料湖存放區的儲存空間 Blob 的資料流資料使用 Azure 資料流分析](data-lake-store-stream-analytics.md)。

## <a name="use-data-lake-store-with-power-bi"></a>使用 Power bi 資料湖存放區

您可以使用 Power BI 資料匯入資料湖存放帳戶來分析並以視覺化方式呈現資料。 如需詳細資訊，請參閱[使用 Power BI 的資料湖存放區中的分析資料](data-lake-store-power-bi.md)。

## <a name="use-data-lake-store-with-data-catalog"></a>使用資料目錄的使用資料湖存放區

您可以將 Azure 資料目錄，讓資料可搜尋整個組織登錄資料湖存放區。 如需詳細資訊請參閱[註冊 Azure 資料目錄] 中的資料湖存放區](data-lake-store-with-data-catalog.md)。


## <a name="see-also"></a>另請參閱

- [Azure 資料湖存放區的概觀](data-lake-store-overview.md)
- [開始使用] 入口網站的資料湖存放區](data-lake-store-get-started-portal.md)
- [使用 PowerShell 資料湖存放快速入門](data-lake-store-get-started-powershell.md)  
