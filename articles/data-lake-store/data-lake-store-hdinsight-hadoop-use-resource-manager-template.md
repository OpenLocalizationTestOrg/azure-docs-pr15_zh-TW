<properties
   pageTitle="Azure 資料湖存放區使用資源管理員範本建立 HDInsight 叢集 |Microsoft Azure"
   description="使用 Azure 資源管理員範本來建立及使用 HDInsight 叢集 Azure 資料湖存放區"
   services="data-lake-store,hdinsight"
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
   ms.date="10/21/2016"
   ms.author="nitinme"/>

# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>使用資料湖存放使用 Azure 資源管理員範本建立 HDInsight 叢集

> [AZURE.SELECTOR] - [使用入口網站](data-lake-store-hdinsight-hadoop-use-portal.md) - [使用 PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md) - [使用資源管理員](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)

瞭解如何使用 Azure 資源管理員範本來設定 HDInsight 叢集有權存取 Azure 資料湖存放區。 在這個版本的一些重要考量事項︰

-   **為火花叢集 (Linux) 和 Hadoop/大量叢集 （Windows 和 Linux）**、 資料湖存放區只可為額外的儲存空間帳戶。 預設儲存帳戶，例如叢集仍可 Azure 儲存體二進位大型物件 (WASB)。

-   以預設儲存空間或額外的儲存空間可**針對 HBase 叢集 （Windows 和 Linux）**、 資料湖存放區。

> [AZURE.NOTE] 注意的一些重要事項。
>
> - 僅適用於 HDInsight 版本 3.2 捨位及 3.4 （適用於 Windows，以及 Linux Hadoop、 HBase，以及大量叢集） 時，若要建立 HDInsight 叢集有權存取資料湖存放區的選項。 針對 linux 火花叢集，這個選項只適 HDInsight 3.4 叢集上。
>
> - 如上所述，資料湖存放使用做為預設儲存空間的某些叢集類型 (HBase) 和其他叢集類型 （Hadoop、 火花大量） 額外的儲存空間。 使用資料湖存放為額外的儲存空間帳戶並不會影響效能或讀/寫儲存的能力。 在此案例中資料湖存放位置作為額外儲存空間，叢集相關檔案 （例如記錄等） 會寫入到預設儲存空間 (Azure Blob)，而您想要處理的資料可以儲存資料湖存放帳戶。
>

本文中，我們會提供資料湖存放區的 Hadoop 叢集為額外的儲存空間。

## <a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

-   **Azure 訂閱**。 請參閱[取得 Azure 免費試用版](https://azure.microsoft.com/pricing/free-trial/)。

-   **Azure PowerShell 1.0 或更大**。 瞭解[如何安裝和設定 PowerShell 的 Azure](../powershell-install-configure.md)。

- **Azure Active Directory 服務主要**。 在本教學課程中的步驟會提供說明如何建立 Azure AD 服務主要。 不過，您必須是 Azure AD 管理員，才能建立服務主要。 如果您是 Azure AD 管理員，您可以略過此必要條件，並繼續教學課程。
    
    **如果您不是 Azure AD 系統管理員**，您無法執行建立服務主要所需的步驟。 在這種情況下，您的 Azure AD 系統管理員必須先建立服務主要才能建立 HDInsight 叢集資料湖存放區。 此外，服務主要必須建立使用的認證，所述，[建立憑證的主體的服務](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate)。

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>建立 HDInsight 叢集 Azure 資料湖存放區

資源管理員範本，並使用範本的必要條件，可在[部署新的資料湖存放區的 HDInsight Linux 叢集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)GitHub 上。 依照指示在此連結提供建立 HDInsight 叢集 Azure 資料湖存放區為額外的儲存空間。

在 [提及] 連結的指示進行要求 PowerShell。 這些指示您開始之前，請確定您登入您的 Azure 帳戶。 從您的桌面，開啟新的 PowerShell 的 Azure 視窗，然後輸入下列程式碼片段。 當畫面提示您登入，請確定您登入為其中一項訂閱 admininistrators/擁有者︰

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>上傳至 Azure 資料湖存放區的範例資料

資源管理員範本建立新的資料湖存放帳戶，並將它 HDInsight 叢集關聯。 您現在必須上載資料湖存放區的一些範例資料。 您需要更新版本中執行的工作，從 HDInsight 叢集存取資料湖存放區中的資料的教學課程此資料。 如需如何上傳資料的相關指示，請參閱[上傳檔案至您的資料湖存放區](data-lake-store-get-started-portal.md#uploaddata)。 如果您要尋找的上傳的一些範例資料，您可以取得[Azure 資料湖給存放庫](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData)**政策救護車資料**的資料夾。

## <a name="set-relevant-acls-on-the-sample-data"></a>設定相關 Acl 的範例資料

若要確保您上傳的範例資料是從 HDInsight 叢集存取，您必須確定 Azure AD 應用程式是用來建立 HDInsight 叢集和資料湖存放區之間的身分識別有權存取您嘗試存取的檔案/資料夾。 若要這麼做，請執行下列步驟。

1.  尋找與 HDInsight 叢集和資料湖存放相關聯的 Azure AD 應用程式的名稱。 若要尋找名稱的其中一個方法是開啟使用資源管理員範本所建立的 HDInsight 叢集刀**叢集 AAD 身分識別**] 索引標籤，然後按一下尋找**服務主要顯示名稱**值。

2.  現在，您想要從 HDInsight 叢集存取檔案/資料夾提供存取此 Azure AD 應用程式。 若要設定正確 Acl 資料湖存放區中檔案/資料夾，請參閱[資料湖存放區中的保護資料](data-lake-store-secure-data.md#assign-users-or-security-group-as-acls-to-the-azure-data-lake-store-file-system)。

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>使用資料湖存放區 HDInsight 叢集上執行測試工作

您已設定 HDInsight 叢集之後，您可以執行叢集測試 HDInsight 叢集可以存取資料湖存放區上的測試工作。 若要這麼做，我們會執行建立表格，使用您上傳的舊版資料湖存放區的範例資料的範例登錄區工作。

### <a name="for-a-linux-cluster"></a>Linux 叢集

本節中您必須將 SSH 到叢集，並執行範例登錄區查詢。 Windows 不提供的內建的 SSH 用戶端。 我們建議使用**PuTTY**，可以從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載。

如需有關如何使用 PuTTY 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

1.  連線之後，請使用下列命令以啟動登錄區 CLI:

    ```
    hive
    ```

2.  使用 CLI，輸入要建立新資料表命名**車輛**資料湖存放區中使用的範例資料的下列陳述式︰

    ```
    DROP TABLE vehicles;
    CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
    SELECT * FROM vehicles LIMIT 10;
    ```

    您應該會看到類似以下的輸出︰

    ```
    1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
    1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
    1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
    1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
    1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
    1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
    1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
    1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
    1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
    1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
    ```

### <a name="for-a-windows-cluster"></a>Windows 叢集

使用下列 cmdlet，以執行登錄區查詢。 在這個查詢我們會從資料湖存放區中的資料建立表格，然後再執行建立表格的 [選取查詢。

```
$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials
```

這會有下列輸出。 **ExitValue**輸出中的 0 的建議工作已順利完成。

```
Cluster         : hdiadlcluster.
HttpEndpoint    : hdiadlcluster.azurehdinsight.net
State           : SUCCEEDED
JobId           : job_1445386885331_0012
ParentId        :
PercentComplete :
ExitValue       : 0
User            : admin
Callback        :
Completed       : done
```

擷取從工作的成果，使用下列 cmdlet:

```
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials
```

工作輸出類似下列各項︰

```
1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
```

## <a name="access-data-lake-store-using-hdfs-commands"></a>Access 資料湖存放區使用 HDFS 命令

一旦您已設定 HDInsight 叢集使用資料湖存放區，您可以使用 HDFS 命令介面指令存取存放區。

### <a name="for-a-linux-cluster"></a>Linux 叢集

在這個區段您將叢集會 SSH 及執行 HDFS 命令。 Windows 不提供的內建的 SSH 用戶端。 我們建議使用**PuTTY**，可以從[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)下載。

如需有關如何使用 PuTTY 的詳細資訊，請參閱[使用 SSH Linux 為基礎的 Hadoop HDInsight 從 Windows 上使用](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md)。

連線後，使用下列 HDFS 檔案系統命令清單資料湖存放區中的檔案。

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

此應該列出您上傳的舊版資料湖存放區的檔案。

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

您也可以使用`hdfs dfs -put`] 命令以某些檔案上傳到 [資料湖 Store]，然後使用`hdfs dfs -ls`確認是否已成功上傳的檔案。

### <a name="for-a-windows-cluster"></a>Windows 叢集

1.  登入新的[Azure 入口網站](https://portal.azure.com)。

2.  按一下 [**瀏覽**]、 按一下**HDInsight 叢集**，，然後按一下您建立的 HDInsight 叢集。

3.  在叢集刀中，按一下**遠端桌面**]，然後在 [**遠端桌面**刀中，按一下 [**連線**。

    ![將 HDI 叢集遠端](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png)

    出現提示時，輸入您的遠端桌面使用者所提供的認證。

4.  在遠端工作階段，啟動 Windows PowerShell，並使用 HDFS 檔案系統命令清單 Azure 資料湖存放區中的檔案。

    ```
    hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
    ```

    此應該列出您上傳的舊版資料湖存放區的檔案。

    ```
    15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
    Found 1 items
    -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv
    ```

    您也可以使用`hdfs dfs -put`] 命令以某些檔案上傳到 [資料湖 Store]，然後使用`hdfs dfs -ls`確認是否已成功上傳的檔案。

## <a name="next-steps"></a>後續步驟

-   [Azure 儲存體 Blob 將資料複製到資料湖存放區](data-lake-store-copy-data-wasb-distcp.md)
