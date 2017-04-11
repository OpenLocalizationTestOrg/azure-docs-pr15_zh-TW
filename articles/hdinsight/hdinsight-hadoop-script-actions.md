<properties
    pageTitle="指令碼動作開發與 HDInsight |Microsoft Azure"
    description="瞭解如何自訂指令碼動作 Hadoop 叢集。 若要安裝 Hadoop 叢集上執行的其他軟體或變更叢集上所安裝的應用程式的設定，可指令碼的巨集指令。"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>開發 HDInsight Windows 型叢集的指令碼動作指令碼

瞭解如何撰寫指令碼的巨集指令的指令碼的 HDInsight。 使用指令碼動作指令碼的資訊，請參閱[自訂 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster.md)。 如為 Linux 型 HDInsight 叢集相同的文章，請參閱[HDInsight 開發指令碼動作指令碼](hdinsight-hadoop-script-actions-linux.md)。

> [AZURE.NOTE] 這份文件中的資訊是 Windows 型 HDInsight 叢集特定項目。 使用 Windows 型叢集指令碼動作的詳細資訊，請參閱[使用 HDInsight (Linux) 的指令碼動作開發](hdinsight-hadoop-script-actions-linux.md)。


若要安裝 Hadoop 叢集上執行的其他軟體或變更叢集上所安裝的應用程式的設定，可指令碼的巨集指令。 指令碼動作部署 HDInsight 叢集時, 叢集節點執行的指令碼，而且之後，叢集節點完成 HDInsight 設定，它們會執行。 指令碼的巨集指令執行 [系統管理員帳戶權限下，並提供叢集節點的完整存取權限。 每個叢集能提供清單的指令碼所指定的順序中所要執行的動作。

> [AZURE.NOTE] 如果您遇到下列錯誤訊息︰
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> 這是因為您未包含協助方法。  請參閱[協助自訂指令碼的方法](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts)。

## <a name="sample-scripts"></a>指令碼範例

在 Windows 作業系統上建立 HDInsight 叢集，指令碼動作是 Azure PowerShell 指令碼。以下是範例指令碼的設定網站的設定檔︰

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

四個參數，設定檔案名稱]、 [您想要修改，您想要設定，的值] 屬性與描述，則會將指令碼。 例如︰

    hive-site.xml hive.metastore.client.socket.timeout 90

這些參數會設定為 90 hive.metastore.client.socket.timeout 值登錄區 site.xml 檔案中。  預設值為 60 秒。

此範例指令碼也會位於[https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1)。

HDInsight 提供數種指令碼 HDInsight 叢集上安裝的附加元件︰

名稱 | 指令碼
----- | -----
**安裝火花** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1。 請參閱[安裝並使用激起 HDInsight 叢集上][hdinsight-install-spark]。
**安裝 R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1。 請參閱[安裝並使用 R HDInsight 叢集上的][hdinsight-r-scripts]。
**安裝 Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1。 請參閱[安裝並使用 Solr 上 HDInsight 叢集](hdinsight-hadoop-solr-install.md)。
- **安裝 Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1。 請參閱[安裝並使用 Giraph 上 HDInsight 叢集](hdinsight-hadoop-giraph-install.md)。

從 Azure 入口網站中，PowerShell 的 Azure，或是使用 HDInsight.NET SDK，可以部署指令碼的巨集指令。  如需詳細資訊，請參閱[使用指令碼的巨集指令的自訂 HDInsight 叢集][hdinsight-cluster-customize]。

> [AZURE.NOTE] 在 [指令碼範例工作 HDInsight 叢集版本 3.1 的只或上方。 如需有關 HDInsight 叢集版本的詳細資訊，請參閱[HDInsight 叢集版本](hdinsight-component-versioning.md)。





## <a name="helper-methods-for-custom-scripts"></a>自訂指令碼協助方法

指令碼動作協助方法是公用程式撰寫自訂指令碼時，您可以使用。 這些都定義在[https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)，並可以包含在您使用下列的指令碼︰

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

以下是協助方法所提供的這個指令碼︰

協助程式方法 | 描述
-------------- | -----------
**儲存 HDIFile** | 下載檔案從指定的統一資源識別項 (URI) 指派給叢集 Azure VM 節點相關聯的本機磁碟上的位置。
**展開 HDIZippedFile** | 解壓縮壓縮的檔案。
**呼叫 HDICmdScript** | 從 cmd.exe 執行指令碼。
**撰寫 HDILog** | 輸出寫入用的指令碼的巨集指令的自訂指令碼。
**取得服務** | 取得服務清單，在電腦上執行指令碼執行的位置。
**取得服務** | 輸入特定的服務名稱，以取得 [特定的服務的詳細的資訊 （服務名稱，處理識別碼狀態等） 上執行指令碼的位置。
**取得 HDIServices** | 取得 HDInsight 服務電腦上執行指令碼執行的位置清單。
**取得 HDIService** | 輸入特定 HDInsight 服務名稱，以取得 [特定的服務的詳細的資訊 （服務名稱，處理識別碼狀態等） 上執行指令碼的位置。
**取得 ServicesRunning** | 取得服務的電腦上執行指令碼的執行位置的清單。
**取得 ServiceRunning** | 檢查特定服務 （按名稱） 正在執行的電腦上執行指令碼的位置。
**取得 HDIServicesRunning** | 取得 HDInsight 服務電腦上執行指令碼執行的位置清單。
**取得 HDIServiceRunning** | 檢查是否 （按名稱） 的特定 HDInsight 服務上執行的電腦上執行指令碼的位置。
**取得 HDIHadoopVersion** | 取得 Hadoop 指令碼的執行位置的電腦上安裝的版本。
**測試 IsHDIHeadNode** | 檢查執行指令碼的位置的電腦是否標頭的節點。
**測試 IsActiveHDIHeadNode** | 檢查執行指令碼的位置的電腦是否是作用中的主節點。
**測試 IsHDIDataNode** | 檢查執行指令碼的位置的電腦是否資料節點。
**編輯 HDIConfigFile** | 編輯設定檔登錄區 site.xml、 核心 site.xml、 hdfs site.xml、 mapred site.xml 或 yarn site.xml。


## <a name="best-practices-for-script-development"></a>指令碼開發的最佳作法

當您開發自訂指令碼 HDInsight 叢集時，有幾個謹記的最佳作法︰

- 檢查 Hadoop 版本

    HDInsight 版本 3.1 (Hadoop 2.4) 支援叢集上安裝自訂元件使用指令碼的巨集指令的上方。 在您的自訂指令碼，您必須使用**取得 HDIHadoopVersion**協助程式方法來檢查 Hadoop 版本，才能繼續執行其他工作的指令碼。


- 提供穩定指令碼資源的連結

    使用者請確認所有的指令碼和自訂的叢集中使用其他成品保持提供叢集的存留時間及期間請勿變更這些檔案的版本。 如果需要重新影像叢集中的節點，這些資源所需。 最佳作法是下載並封存使用者控制項的儲存空間帳戶中的所有項目。 這可能是預設儲存帳戶或任何其他自訂叢集部署時間指定的儲存空間帳戶。
    在 [火花和 R 自訂叢集範例提供的文件，例如，我們資源的本機複本中進行此儲存帳戶︰ https://hdiconfigactions.blob.core.windows.net/。


- 請確定叢集自訂指令碼冪

    您必須在叢集生命週期將會重新影像處理 HDInsight 叢集節點的預期。 重新影像處理叢集時，會執行叢集自訂指令碼。 這個指令碼必須設計要在意義上，在重新影像、 指令碼應該確保叢集會傳回相同的冪自訂指令碼執行一開始建立叢集第一次後，只是中的狀態。 例如，如果自訂指令碼上安裝應用程式，在 D:\AppLocation 其第一個執行，請在每一個後續執行，在重新影像、 指令碼應該應用程式是否已存在於 D:\AppLocation 位置之前與其他的後續步驟指令碼。


- 安裝自訂的元件的最佳的位置

    重新影像處理叢集節點時，表示 C:\ 資源磁碟機和 D:\ 系統磁碟機可重新格式化、 遺失資料和這些磁碟機已安裝的應用程式的結果。 也會發生此問題如果叢集一部分 Azure 虛擬機器 (VM) 節點故障時，會由新的節點。 您可以在 D:\ 磁碟機或叢集 C:\apps 位置安裝元件。 保留 C:\ 磁碟機上所有其他的位置。 指定應用程式或文件庫的位置安裝叢集自訂指令碼中的位置。


- 確保架構的可用性

    Hdinsight 都有主動被動架構高可用性，其中一個主節點是作用中的模式 （位置執行 HDInsight 服務） 和其他主節點是在備用模式下 （在哪些 HDInsight 服務並未執行）。 如果 HDInsight 服務會中斷，節點切換作用中] 及 [被動式模式。 如果指令碼動作用來在這兩個標頭節點高可用性安裝服務，請注意 HDInsight 容錯移轉機制不會自動連這些使用者安裝的服務。 HDInsight 應該是高度可用的標頭節點，因此已使用者安裝的服務必須有自己的容錯移轉機制，如果在主動被動模式中，或者是在 [作用中作用中的模式。

    HDInsight 指令碼動作命令執行兩個標頭節點上時不對節點角色指定為*ClusterRoleCollection*參數中的值。 在當您設計自訂指令碼，請確認您的指令碼並知道這項設定。 您不應該執行相同的服務已安裝並啟動其中一個標頭節點，及他們最後競爭彼此的問題。 此外，請注意期間重新影像]，將會遺失資料，透過指令碼動作安裝軟體都有彈性，例如事件。 若要使用的分散到多個節點的高度可用資料應設計應用程式。 請注意，可以同時重新影像為 1/5 / 叢集節點。


- 設定自訂的元件，可使用 Azure Blob 儲存體

    您在叢集節點安裝的自訂元件可能使用 Hadoop 分散式檔案系統 (HDFS) 儲存空間的預設設定。 您應該變更設定，請改為使用 Azure Blob 儲存體。 在叢集重新圖像，取得格式化 HDFS 檔案系統，您會遺失任何那里儲存的資料。 使用 Azure Blob 儲存體改為可確保將全部保留您的資料。

## <a name="common-usage-patterns"></a>一般使用模式

本節提供實作撰寫您自己的自訂指令碼時可能會遇到的常見使用模式的一些指引。

### <a name="configure-environment-variables"></a>設定環境變數

通常在指令碼動作開發中，您會覺得需要設定環境變數。 例如，最可能的案例是當您從外部網站下載二進位、 將其安裝於叢集，並新增安裝至您的 'PATH' 環境變數的位置。 下列程式碼片段顯示如何設定環境變數中的自訂指令碼。

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

此陳述式環境變數**MDS_RUNNER_CUSTOM_CLUSTER**設定為 「 true 」 的值，也會將此變數是全電腦的範圍。 有時很重要設定環境變數時，會在適當的範圍 – 電腦或使用者。 請參閱[以下][1]如需有關設定環境變數。

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>儲存自訂指令碼的位置

用來自訂叢集的指令碼必須先將是在預設儲存空間的叢集帳戶或任何其他儲存帳戶的唯讀公用容器中。 如果您的指令碼存取處的資源這些必須公開存取 (至少公用唯讀狀態)。 舉個例說，您可以存取的檔案，並將它使用 SaveFile HDI 命令儲存。

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

在此範例中，您必須確定容器中儲存的帳戶 」 somestorageaccount' ' somecontainer 是公開存取。 否則，指令碼會將 「 找不到 」 的例外狀況而失敗。

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>新增 AzureRmHDInsightScriptAction cmdlet 傳遞的參數

若要新增 AzureRmHDInsightScriptAction cmdlet 傳遞多個參數，您需要設定格式的字串值，以包含所有參數的指令碼。 例如︰

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

或

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>例外狀況失敗的叢集部署

如果您想要取得正確的通知叢集自訂 fact 沒有成功，請務必引發例外狀況而失敗叢集建立。 比方說，您可能要處理檔案，如果有的話，處理檔案不存在錯誤情況。 如此可確保正常地結束指令碼，並正確已知叢集狀態。 下列程式碼片段會提供範例如何達到這個目的︰

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

在此程式碼片段，如果檔案沒有，它會導致的狀態的指令碼實際正常地結束之後列印錯誤訊息，而叢集達到假設它是 「 成功 」 完成叢集自訂程序的執行狀態。 如果您想要精確地收到基本上叢集自訂交易的無法如預期般由於遺失的檔案，更適合引發例外狀況而失敗叢集自訂步驟。 若要達到此目標您必須使用下列的程式碼片段範例。

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>部署指令碼的巨集指令的檢查清單
以下是我們原本準備部署這些指令碼時的步驟︰

1. 將檔案包含自訂指令碼部署期間叢集節點的可存取的位置。 這可能是任何預設或指定一次叢集部署或任何其他的公開存取存放容器的額外儲存空間帳戶。
2. 新增到指令碼，以確保他們執行 idempotently，以便指令碼可以多次執行相同的節點上檢查。
3. 使用**寫入輸出**Azure PowerShell cmdlet 來列印 STDOUT，以及 STDERR。 不要使用**寫入主機**。
4. 使用暫存檔案資料夾，例如 $env: TEMP，若要保留下載指令碼使用的檔案，然後清除這些後執行指令碼。
5. 安裝自訂的軟體，只會在 D:\ 或 C:\apps。 保留時不應該用於 c 磁碟機上的其他位置。 請注意，安裝 C:\apps 資料夾以外的 c 磁碟機上的檔案可能會導致安裝失敗期間重新節點的圖像。
6. OS 層級設定] 或 [Hadoop 服務設定檔已變更，您可能會想要重新啟動 HDInsight 服務，讓他們可以選擇任何 OS 層級設定，例如設定中的指令碼的環境變數。

## <a name="debug-custom-scripts"></a>為自訂指令碼偵錯

儲存指令碼錯誤記錄，以及其他輸出，在您為其建立叢集指定預設儲存帳戶。 含有名稱的表格中儲存記錄*u < \cluster-name-fragment >< \time-stamp > setuplog*。 這些是彙總有叢集指令碼所執行的節點 （標頭節點和工作者節點） 的所有記錄的記錄。
使用 Visual Studio HDInsight 工具就能輕鬆檢查記錄檔。 安裝工具]，請參閱[開始使用 Visual Studio Hadoop HDInsight 工具](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**若要檢查使用 Visual Studio 的記錄檔**

1. 開啟 Visual Studio。
2. 按一下 [**檢視**]，然後按一下 [**伺服器總管]**。
3. 以滑鼠右鍵按一下 「 Azure 」，按一下 [連線至**Microsoft Azure 訂閱**，，然後輸入您的認證。
4. 展開**儲存空間**，展開用來做為預設檔案系統的 Azure 儲存體帳戶、 展開**資料表**，然後按兩下資料表名稱。


您可以將叢集節點，若要查看同時也遠端 STDOUT 和 STDERR 自訂指令碼。 每個節點的記錄是只針對該節點，並且登入**C:\HDInsightLogs\DeploymentAgent.log**。 這些記錄檔記錄所有輸出的自訂指令碼。 範例記錄的程式碼片段火花指令碼動作看起來像這樣︰

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


在此記錄，則清除火花指令碼的巨集指令的已命名 HEADNODE0 VM 上執行的任何例外狀況執行期間。

執行失敗發生時，它描述的輸出也會包含在此記錄檔。 在這些記錄中所提供的資訊應在偵錯指令碼發生的問題，可能會很有幫助。


## <a name="see-also"></a>另請參閱

- [自訂 HDInsight 叢集使用指令碼的巨集指令][hdinsight-cluster-customize]
- [安裝並使用火花 HDInsight 叢集上][hdinsight-install-spark]
- [安裝並使用 R HDInsight 叢集上][hdinsight-r-scripts]
- [安裝並使用 Solr 上 HDInsight 叢集](hdinsight-hadoop-solr-install.md)。
- [安裝並使用 Giraph 上 HDInsight 叢集](hdinsight-hadoop-giraph-install.md)。

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
