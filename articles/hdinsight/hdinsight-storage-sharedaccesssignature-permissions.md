<properties
pageTitle="限制 HDInsight 存取使用共用的 Access 簽章的資料"
description="瞭解如何使用共用的 Access 簽章限制 HDInsight 存取儲存在儲存 Azure blob 的資料。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>若要限制存取與 HDInsight 的資料使用 Azure 儲存體共用 Access 簽章

HDInsight 使用儲存空間 Azure Blob 資料的儲存空間。 雖然 HDInsight 都必須有做為預設儲存空間叢集 blob 的完整功能，您可以限制權限，叢集使用其他二進位大型物件中儲存的資料。 例如，您可能要將部分資料為唯讀。 您可以使用共用的 Access 簽章。

共用 Access 簽章 (SA) 是一種 Azure 儲存體帳戶功能，可讓您限制存取資料。 例如，提供唯讀資料的存取權。 在此文件中，您將學習如何使用 SA 從 HDInsight blob 容器啟用讀取和清單專用的存取權。

##<a name="requirements"></a>需求

* Azure 的訂閱

* C# 或 Python。 C# 程式碼範例，提供為 Visual Studio 解決方案。

    * Visual Studio 必須版 2013年或 2015
    
    * Python 必須 2.7 或更新版本

* Linux 為基礎的 HDInsight 叢集或[PowerShell 的 Azure] [ powershell] -如果您有現有的 Linux 型叢集，您可以使用 Ambari 叢集加入共用 Access 簽章。 如果沒有出現，您可以使用 PowerShell 的 Azure 來建立新的叢集及叢集建立期間新增共用的 Access 簽章。

* 從[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)範例檔案。 此存放庫具備下列項目︰

    * 可以建立存放容器、 儲存的原則，以及 SA 用於 HDInsight Visual Studio 專案
    
    * 可以建立存放容器、 儲存的原則，以及 SA 用於 HDInsight Python 指令碼
    
    * PowerShell 指令碼，可以建立新的 HDInsight 叢集，並將它設定為使用 SA。

##<a name="shared-access-signatures"></a>共用的 Access 簽章

有兩種共用 Access 簽章︰

* 臨機操作︰ 的開始時間、 到期時間和權限的 sa 是全部指定 SA URI 上 （或暗示，則開始時間的大小寫）。

* 儲存存取原則︰ 儲存的存取原則定義在資源容器-blob 容器、 表格、 佇列中或檔案共用-，並可用於管理一或多個共用的 access 簽章的限制式。 當您建立 SA 關聯與儲存的存取原則時，SA 會繼承限制-的開始時間、 到期時間和權限-儲存的存取原則定義。

兩個表單之間的差異是很重要的一種主要的情況︰ 撤銷。 SA 是 URL 取得 SA 的任何人均可以使用，無論誰開始要求。 如果 SA 公開發佈，其可讓任何人在全世界的哪個。 散發 SA 是有效的直到其中四個項目會發生的情況︰

1. 達到 SA 上指定的到期時間。

2. （如果有參照的儲存的存取原則，與指定的到期時間） 達到 SA 參照的儲存的存取原則上指定的到期時間。 [可能是因為超過時間間隔，或是您已修改儲存的存取原則具有過去，這是一種方式撤銷 SA 的到期時間。

3. 刪除 SA 參照的儲存的存取原則，已撤銷 SA 另一種方法。 請注意，如果您重新建立具有相同名稱的儲存的存取原則，所有現有 SA 權杖會再次有效根據儲存的存取原則 （假設的 SA 的到期時間超過） 相關聯的權限。 如果您想要撤銷 SA，請務必使用不同的名稱，如果您重新建立存取原則，以在未來的到期時間。

4. 用來建立 SA 帳戶金鑰會重新產生。 請注意，執行此動作會使用該帳戶金鑰失敗來驗證其會更新為使用另一個有效的帳戶金鑰或重新產生新的帳戶金鑰]，直到所有應用程式元件。

> [AZURE.IMPORTANT] 共用的 access 簽名 URI 用來建立簽章的帳戶金鑰功能相關聯，關聯儲存存取原則，（如果有的話）。 如果有不指定任何已儲存的存取原則，撤銷共用的 access 簽章的唯一方法是變更 [帳戶金鑰]。 

建議您永遠使用儲存的存取原則，讓您可以撤銷簽章，或延伸的到期日，視需要。 此文件使用中的步驟儲存產生 SA 存取原則。

如需有關共用 Access 簽章的詳細資訊，請參閱[瞭解 SA 模型](../storage/storage-dotnet-shared-access-signature-part-1.md)。

##<a name="create-a-stored-policy-and-generate-a-sas"></a>建立儲存的原則，並產生 SA

目前您必須以程式設計方式建立儲存的原則。 您可以找到 C# 和 Python 範例在[https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature)建立儲存的原則和 SA。

###<a name="create-a-stored-policy-and-sas-using-c"></a>建立儲存的原則和 SA 使用 C\#

1. 在 Visual Studio 中開啟的方案。

2. 在方案總管中，以滑鼠右鍵按一下__SASToken__專案，然後選取 [__內容]__。

3. 選取 [__設定__]，並新增下列項目] 值︰

    * StorageConnectionString︰ 您想要建立的已儲存的原則和 SA 儲存帳戶之連線字串。 格式應該是`DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey`位置`myaccount`是指您儲存的帳戶名稱和`mykey`是儲存帳戶金鑰。
    
    * ContainerName︰ 在您想要限制存取儲存帳戶容器。
    
    * SASPolicyName︰ 若要使用儲存的原則會建立的名稱。
    
    * FileToUpload︰ 將容器上傳檔案的路徑。
    
4. 執行專案。 主控台會出現一個視窗，並產生 SA 後，將會顯示類似下列資訊︰

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    當您需要此儲存帳戶關聯 HDInsight 叢集時，請儲存 SA 原則權杖。 您也必須儲存體帳戶名稱，然後容器名稱。
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>建立儲存的原則和使用 Python SA

1. 開啟 SASToken.py 檔案，並變更為以下值︰

    * 原則\_名稱︰ 使用儲存的原則，將會建立的名稱。
    
    * 儲存\_帳戶\_名稱︰ 儲存帳戶的名稱。
    
    * 儲存\_帳戶\_索引鍵︰ 儲存帳戶的金鑰。
    
    * 儲存\_容器\_名稱︰ 您想要限制存取的儲存空間帳戶中的容器。
    
    * 範例\_檔案\_路徑︰ 將容器上傳檔案的路徑

2. 執行指令碼。 指令碼完成時，它會顯示 SA 權杖如下︰

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    當您需要此儲存帳戶關聯 HDInsight 叢集時，請儲存 SA 原則權杖。 您也必須儲存體帳戶名稱，然後容器名稱。
    
##<a name="use-the-sas-with-hdinsight"></a>使用 HDInsight SA

當建立 HDInsight 叢集，您必須指定主要儲存帳戶，您可以選擇指定額外儲存空間的帳戶。 這兩種方式新增儲存空間的要求儲存帳戶及容器所使用的完整存取權。

限制存取至容器中使用共用的 Access 簽章，您必須將自訂項目新增至叢集__核心網站__設定。

* 針對__Windows 型__或__Linux 型__HDInsight 叢集，您可以在使用 PowerShell 叢集建立期間。

* 針對__Linux 型__HDInsight 叢集，您可以變更使用 Ambari 叢集建立之後的設定。

###<a name="create-a-new-cluster-that-uses-the-sas"></a>建立新的叢集使用 SA

建立使用 SA HDInsight 叢集的範例中所包含`CreateCluster`存放庫的目錄。 若要使用它，請使用下列步驟︰

1. 開啟`CreateCluster\HDInsightSAS.ps1`文字編輯器中的檔案及修改文件的開頭下列的值。

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    例如，變更`'mycluster'`叢集您想要建立的名稱。 建立的儲存空間帳戶和 SA 權杖時，SA 值應該相符的上一個步驟中的值。
    
    一次您已變更的值，儲存檔案。

1. 開啟新的 PowerShell 的 Azure 提示。 如果您不熟悉 PowerShell 的 Azure，或未安裝，請參閱[安裝及設定 PowerShell 的 Azure][powershell]。

2. 從提示時，使用下列項目會驗證 Azure 訂閱︰

        Login-AzureRmAccount
    
    出現提示時，您 Azure 訂閱的帳戶登入。
    
    如果您的登入有多個 Azure 訂閱相關聯，您可能需要使用`Select-AzureRmSubscription`以選取您想要使用的訂閱。

2. 從提示時，變更目錄`CreateCluster`HDInsightSAS.ps1 檔案所在的目錄。 然後使用下列命令以執行指令碼
        
        .\HDInsightSAS.ps1
    
    執行指令碼，它會登輸出 PowerShell 提示中建立資源群組和儲存的帳戶。 然後會提示您輸入 HDInsight 叢集 HTTP 使用者。 這是用來安全叢集 HTTP/s 存取的使用者帳戶。
    
    如果您正在建立 Linux 叢集，您也會提示 SSH 使用者帳戶名稱和密碼。 這用來從遠端叢集的登入。
    
    > [AZURE.IMPORTANT] 出現提示時 HTTP/s 或 SSH 使用者名稱和密碼，您必須提供符合下列條件的密碼︰
    >
    > - 必須至少 10 個字元的長度
    > - 必須包含至少一個數字
    > - 必須包含至少一個非英數字元
    > - 必須包含至少一個大寫或小寫字母


需要時，以取得此指令碼來完成，通常約 15 分鐘。 指令碼完成時沒有任何錯誤，已建立叢集。

###<a name="update-an-existing-cluster-to-use-the-sas"></a>更新現有的叢集使用 SA

如果您有現有的 Linux 型叢集，您可以新增__核心網站__組態 SA 使用下列步驟︰

1. 開啟叢集 Ambari 網頁 UI。 對此頁面的地址是 https://YOURCLUSTERNAME.azurehdinsight.net。 出現提示時，進行驗證叢集使用管理員的名稱 （系統），並建立叢集時，您使用的密碼。

2. 從 Ambari 網頁 UI 的左側，選取__HDFS__ ，然後選取 [__設定__] 索引標籤，在頁面中間。

3. 選取 [__進階__] 索引標籤，然後捲動，直到您找到 [__自訂核心網站__] 區段。

4. 展開 [__自訂核心網站__] 區段中，然後捲動到結尾，選取 [__新增屬性...__ ] 連結。 __索引鍵__和__值__欄位中使用下列的值︰

    * __索引鍵__︰ fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __值__︰ SA 傳回由您在先前執行 C# 或 Python 應用程式
    
    您使用與 C# 或 SA 應用程式的容器名稱取代__CONTAINERNAME__ 。 __STORAGEACCOUNTNAME__換成您用來儲存體帳戶名稱。

5. 按一下 [__新增__] 按鈕，將儲存的值，此鍵，然後按一下 [__儲存__] 按鈕，將設定變更儲存]。 出現提示時，加上的描述，變更 （「 新增 SA 儲存 access 」，例如），然後按一下 [__儲存__。

    完成所做的變更，請按一下__[確定]__ 。

    > [AZURE.IMPORTANT] 這會儲存設定的變更，但變更生效之前，您必須重新啟動多個服務。

6. 在 Ambari web 使用者介面中，從左邊的清單中選取__HDFS__ ，然後選取__重新啟動所有____服務動作__下拉式清單右側。 出現提示時，選取__維護模式開啟__，然後選取 __Conform 所有重新啟動 」。

    在頁面左側的清單的 MapReduce2 和 YARN 項目重複此程序。

7. 一旦這些重新啟動，請選取每筆，然後停用維護模式下，從__服務動作__下拉式清單。

##<a name="test-restricted-access"></a>測試限制的存取

若要確認您擁有存取權限，請使用下列方法︰

* 針對__Windows 型__HDInsight 叢集，使用遠端桌面連線到叢集。 如需詳細資訊，請參閱[Connecto 至 HDInsight 使用 RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) 。

    連線之後，請在桌面上使用 [ __Hadoop 命令列__圖示，以開啟命令提示字元。

* 針對__Linux 型__HDInsight 叢集，使用 SSH 叢集連線。 請參閱下列資訊使用 SSH Linux 型叢集︰

    * [使用上從 Linux 與 OS X Unix HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)
    
連線到叢集後，請使用下列步驟以驗證您可以 SA 儲存帳戶只已讀取和清單的項目︰

1. 從提示時，輸入下列項目。 取代__SASCONTAINER__ SA 存放區帳戶建立的容器的名稱。 用於 SA 儲存體帳戶名稱取代__SASACCOUNTNAME__ :

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    這會列出的容器，應該包含已上傳的檔案中的內容建立容器和 SA 時。
    
2. 使用下列命令以確認您可以讀取該檔案的內容。 取代__SASCONTAINER__和__SASACCOUNTNAME__上一個步驟。 取代的上一個命令中顯示的檔案名稱中的__檔案名稱__︰

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    這會列出檔案的內容。
    
3. 若要將檔案下載到本機檔案系統中使用下列︰

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    這會將檔案下載到名稱為__testfile.txt__的本機檔案。

4. 您可以使用下列本機檔案上傳新檔案命名__testupload.txt__ SA 儲存量︰

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    您會收到下列類似訊息︰
    
        put: java.io.IOException
        
    因為讀取 + 僅在清單的儲存位置，就會發生這個錯誤。 將資料放在 [預設儲存空間叢集，這是可寫入使用下列動作︰
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    這次請作業應該順利完成。
    
##<a name="troubleshooting"></a>疑難排解

###<a name="a-task-was-canceled"></a>已取消工作

__發生上述任何症狀__︰ 建立時使用 PowerShell 指令碼，您可能會收到下列錯誤訊息︰

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__原因__︰ 如果您使用的管理員/HTTP 叢集，或使用者 （適用於 Linux 型叢集，） 的密碼，就會發生此錯誤 SSH 使用者。

__解決方案__︰ 使用密碼符合以下準則︰

- 必須至少 10 個字元的長度
- 必須包含至少一個數字
- 必須包含至少一個非英數字元
- 必須包含至少一個大寫或小寫字母

##<a name="next-steps"></a>後續步驟

現在，您已經學會如何限制存取儲存空間新增至 HDInsight 叢集，瞭解使用的資料叢集的其他方法︰

* [使用 HDInsight 的登錄區](hdinsight-use-hive.md)

* [使用 HDInsight 的豬](hdinsight-use-pig.md)

* [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md
