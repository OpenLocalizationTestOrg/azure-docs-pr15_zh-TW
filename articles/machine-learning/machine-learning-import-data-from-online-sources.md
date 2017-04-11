<properties
    pageTitle="將電腦學習 Studio 線上的資料來源的資料匯入 |Microsoft Azure"
    description="如何將資料匯入訓練 Azure 電腦學習 Studio 來自各種線上來源。"
    keywords="匯入資料的資料格式、 資料類型、 資料來源、 訓練資料"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev;garye" />


# <a name="import-data-into-azure-machine-learning-studio-from-various-online-data-sources-with-the-import-data-module"></a>將 Azure 電腦學習 Studio 與匯入資料模組的各種線上的資料來源的資料匯入

本文將說明支援線上資料匯入來自各種來源，並將這些資料來源的資料移至 Azure 電腦學習實驗所需的資訊。

> [AZURE.NOTE] 本文提供有關[匯入資料]的一般資訊[import-data]模組。 如需詳細的資料，您可以存取的類型的資訊時，格式、 參數和常見問題的解答，請參閱模組參照主題[匯入資料]的[import-data]模組。

<!-- -->

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## <a name="introduction"></a>簡介

實驗執行使用[匯入資料]時，您可以從多個連線的資料來源的其中一個存取從 Azure 電腦學習 Studio 內的資料[import-data]模組︰

- 使用 HTTP Web URL
- 使用 HiveQL Hadoop
- Azure blob 儲存體
- Azure 資料表
- Azure SQL 資料庫或 SQL Server Azure VM 上
- 內部部署的 SQL Server 資料庫
- 資料摘要提供者，OData 目前的資料
 
Azure 電腦學習 Studio 中執行的實驗的工作流程包含到畫布上拖曳-和-卸除元件。 若要存取線上的資料來源，將[資料匯入]新增[import-data]模組至您的實驗中，選取**資料來源**]，然後提供存取資料所需的參數。 支援線上的資料來源是分項下表中。 此表格也摘要的支援的檔案格式和用來存取資料的參數值。

請注意，因為這項訓練資料存取實驗正在執行時，它只適用於該實驗。 透過比較，在資料集模組中已儲存的資料可供使用您的工作區中的任何實驗。

> [AZURE.IMPORTANT] 目前，[匯入資料][import-data]與[匯出資料][export-data]模組可以讀取及撰寫資料僅從 Azure 建立使用傳統部署模型的儲存空間。 換句話說，新的 Azure Blob 儲存體帳戶類型，可在作用儲存存取層或製作出酷炫的儲存空間存取層尚未支援。 

> 一般而言，任何 Azure 儲存體帳戶，您已經建立此服務選項成為可用之前則不受影響。 如果您要建立新的帳戶，請選取**傳統**部署模型，或使用資源管理員然後**帳戶類型**，選取 [**一般用途**，而不是**Blob 儲存體**。 

> 如需詳細資訊，請參閱[Azure Blob 儲存體︰ 作用中且製作出酷炫的儲存空間層](../storage/storage-blob-storage-tiers.md)。



## <a name="supported-online-data-sources"></a>支援線上的資料來源
Azure 電腦學習**匯入資料**模組支援下列資料來源︰

資料來源 | 描述 | 參數 |
---|---|---|
透過 HTTP 的 web URL |從任何使用 HTTP 的 web URL 讀取逗點分隔值 (CSV)、 tab 字元分隔的值 (TSV)、 屬性關聯的檔案格式 (ARFF) 及支援向量機器 （SVM 亮） 格式中的資料 | <b>URL</b>︰ 指定的檔案，包括網站 URL] 與 [檔案名稱，以任何副檔名的完整名稱。 <br/><br/><b>資料格式</b>︰ 指定其中一個支援的資料格式︰ CSV、 TSV、 ARFF 或 SVM light。 如果資料有標題列，它用來指定資料行名稱。|
Hadoop/HDFS|讀取中 Hadoop 分散式儲存的資料。 您指定您要使用 HiveQL，類似 SQL 查詢語言的資料。 HiveQL 也可用來彙總資料，並執行之前電腦學習 Studio 中新增資料篩選的資料。|<b>登錄區資料庫查詢</b>︰ 指定登錄區所用的查詢產生資料。<br/><br/><b>HCatalog 伺服器 URI</b> ︰ 指定使用格式*叢集名稱&lt;叢集名稱&gt;。 azurehdinsight.net。*<br/><br/><b>Hadoop 使用者帳戶名稱</b>︰ 指定用來佈建叢集 Hadoop 使用者帳戶名稱。<br/><br/><b>Hadoop 使用者帳戶的密碼</b>︰ 指定佈建叢集時所用的認證。 如需詳細資訊，請參閱[建立 Hadoop 叢集 HDInsight 中](../hdinsight/hdinsight-provision-clusters.md)。<br/><br/><b>輸出資料的位置</b>︰ 指定資料儲存在 Hadoop 分散式檔案系統 (HDFS) 或 Azure。 <br/><ul>如果您在 HDFS 儲存輸出資料，指定 HDFS 伺服器 URI。 （請務必使用 HDInsight 叢集名稱不 HTTPS:// 前置字元）。 <br/><br/>如果您在 Azure 儲存輸出資料，您必須指定 Azure 儲存體帳戶名稱儲存便捷鍵與存放容器名稱。</ul>|
SQL 資料庫 |讀取儲存在 Azure SQL 資料庫或 Azure 虛擬機器上執行的 SQL Server 資料庫的資料。 | <b>資料庫伺服器的名稱</b>︰ 指定要在其執行的資料庫伺服器的名稱。<br/><ul>若 Azure SQL 資料庫中，輸入所產生的伺服器名稱。 通常，它包含表單*&lt;generated_identifier&gt;。 database.windows.net。* <br/><br/>若 SQL server Azure 虛擬機器上輸入*tcp:&lt;虛擬機器 DNS 名稱&gt;、 1433年*</ul><br/><b>資料庫名稱</b>︰ 指定 [伺服器上的 [資料庫名稱。 <br/><br/><b>伺服器的使用者帳戶名稱</b>︰ 指定具有資料庫的存取權限的帳戶的使用者名稱。 <br/><br/><b>伺服器的使用者帳戶密碼</b>︰ 指定的使用者帳戶的密碼。<br/><br/><b>接受任何伺服器的憑證</b>︰ 使用這個選項 （較不安全），如果您想要略過讀取您的資料之前，請檢閱網站的憑證。<br/><br/><b>資料庫查詢</b>︰ 輸入描述您要讀取的資料的 SQL 陳述式。|
內部部署的 SQL 資料庫 |讀取儲存於內部部署的 SQL 資料庫的資料。 | <b>資料閘道</b>︰ 指定它可以在其中存取您的 SQL Server 資料庫的電腦上安裝資料管理閘道器的名稱。 閘道器設定的相關資訊，請參閱[執行進階與 Azure 電腦學習使用內部部署的 SQL server 中的資料分析](machine-learning-use-data-from-an-on-premises-sql-server.md)。<br/><br/><b>資料庫伺服器的名稱</b>︰ 指定要在其執行的資料庫伺服器的名稱。<br/><br/><b>資料庫名稱</b>︰ 指定 [伺服器上的 [資料庫名稱。 <br/><br/><b>伺服器的使用者帳戶名稱</b>︰ 指定具有資料庫的存取權限的帳戶的使用者名稱。 <br/><br/><b>使用者名稱和密碼</b>︰ 按一下 [輸入資料庫認證<b>輸入值</b>。 您可以使用 Windows 整合式驗證或 SQL Server 驗證您的內部部署的 SQL Server 的設定方式而定。<br/><br/><b>資料庫查詢</b>︰ 輸入描述您要讀取的資料的 SQL 陳述式。|
Azure 資料表|從 Azure 儲存體中的表格服務讀取資料。<br/><br/>如果您不常閱讀大量的資料，請使用 [Azure 資料表服務。 它會提供彈性，非關聯式 (NoSQL)、 人連線可調整、 貴，及高度可用儲存空間方案。| 在**匯入資料**的選項變更取決於您要存取資訊公用或私用儲存帳戶需要登入認證。 這是取決於的<b>驗證類型</b>可能有 「 PublicOrSAS 」 或 「 帳戶 」 的值每一個都有自己的參數集。 <br/><br/><b>公用或共用 Access 簽章 (SA) URI</b>︰ 的參數︰<br/><br/><ul><b>表格 URI</b>︰ 為資料表指定公用或 SA URL。<br/><br/><b>指定標題列] 屬性名稱] 掃描</b>︰ 值是<i>TopN</i>掃描指定的列數或<i>ScanAll</i>取得資料表中的所有資料列。 <br/><br/>如果資料是同類型及可預測，建議您選取*TopN* ，並輸入的數字 n。大型的資料表，這可以產生更快速地閱讀時間。<br/><br/>如果資料結構化的屬性，視情況而定根據深度及表格的位置，選擇 [瀏覽所有資料列的 [ *ScanAll* ] 選項。 這可確保您的結果屬性和中繼資料轉換的完整性。<br/><br/></ul><b>私人儲存帳戶</b>︰ 的參數︰ <br/><br/><ul><b>帳戶名稱</b>︰ 指定包含要閱讀的資料表的帳戶的名稱。<br/><br/><b>[帳戶金鑰</b>︰ 指定與帳戶相關聯的儲存空間鍵。<br/><br/><b>資料表名稱</b>︰ 指定包含要讀取的資料之資料表的名稱。<br/><br/><b>標題列] 屬性名稱] 掃描</b>︰ 值是<i>TopN</i>掃描指定的列數或<i>ScanAll</i>取得資料表中的所有資料列。<br/><br/>如果資料是同類型及可預測，我們建議您選取*TopN* ，並輸入的數字 n。大型的資料表，這可以產生更快速地閱讀時間。<br/><br/>如果資料結構化的屬性，視情況而定根據深度及表格的位置，選擇 [瀏覽所有資料列的 [ *ScanAll* ] 選項。 這可確保您的結果屬性和中繼資料轉換的完整性。<br/><br/>|
Azure Blob 儲存體 | 讀取儲存 Azure 儲存體，包括圖像、 非結構化的文字或二進位資料 Blob 服務中的資料。<br/><br/>您可以使用 Blob 服務，來方式公開資料，或是私下儲存應用程式的資料。 您可以從任何位置存取您的資料使用 HTTP 或 HTTPS 的連線。 | 根據您要存取資訊公用或私用儲存帳戶需要登入認證，變更 [**匯入資料**] 模組中的選項。 這是取決於<b>驗證類型</b>可能有的 「 PublicOrSAS 」 或 「 帳戶 」 的值。<br/><br/><b>公用或共用 Access 簽章 (SA) URI</b>︰ 的參數︰<br/><br/><ul><b>URI</b>︰ 指定的儲存空間 blob 公用或 SA URL。<br/><br/><b>檔案格式</b>︰ 指定 Blob 服務中的資料格式。 支援的格式為 CSV、 TSV 及 ARFF。<br/><br/></ul><b>私人儲存帳戶</b>︰ 的參數︰ <br/><br/><ul><b>帳戶名稱</b>︰ 指定包含您想要閱讀的 blob 的帳戶的名稱。<br/><br/><b>[帳戶金鑰</b>︰ 指定與帳戶相關聯的儲存空間鍵。<br/><br/><b>容器、 目錄或 blob 路徑</b>︰ 指定要讀取的資料包含 blob 的名稱。<br/><br/><b>Blob 的檔案格式</b>︰ 指定 blob 服務中的資料格式。 支援的資料格式為 CSV、 TSV、 ARFF、 CSV 指定編碼方式，與 Excel。 <br/><br/><ul>如果 CSV 或 TSV 格式，請務必表示檔案沒有標題列。<br/><br/>若要讀取的 Excel 活頁簿中的資料，您可以使用 [Excel] 選項。 在 [ <i>Excel 資料格式</i>] 選項中，指定是否資料是在 Excel 工作表範圍中，或 Excel 表格中。 在<i>Excel 工作表或內嵌的表格</i>] 選項中，指定工作表] 或 [您想要讀取的資料表的名稱。</ul><br/>|
Data Feed Provider | 從支援的摘要提供者讀取資料。 目前開放式資料通訊協定 (OData) 格式支援。 | <b>資料內容類型</b>︰ 指定 OData 格式。<br/><br/><b>來源 URL</b>︰ 指定的資料摘要的完整 URL。 <br/>將下列 URL，例如 [北風] 樣本資料庫可讀取︰ http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/
