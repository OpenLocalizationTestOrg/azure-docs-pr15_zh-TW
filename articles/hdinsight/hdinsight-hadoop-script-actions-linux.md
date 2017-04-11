<properties
    pageTitle="指令碼動作開發與 Linux 型 HDInsight |Microsoft Azure"
    description="如何自訂 Linux 型 HDInsight 叢集指令碼動作。 指令碼動作是來自訂 Azure HDInsight 叢集指定叢集設定的設定，或安裝其他服務、 工具或其他軟體叢集上的方法。 "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>使用 HDInsight 的指令碼動作開發

指令碼動作是來自訂 Azure HDInsight 叢集指定叢集設定的設定，或安裝其他服務、 工具或其他軟體叢集上的方法。 在叢集建立期間，或在執行叢集，您可以使用指令碼動作。

> [AZURE.NOTE] 這份文件中的資訊是特定 Linux 型 HDInsight 叢集。 使用 Windows 型叢集指令碼動作的詳細資訊，請參閱[使用 HDInsight (Windows) 的指令碼動作開發](hdinsight-hadoop-script-actions.md)。

## <a name="what-are-script-actions"></a>什麼是指令碼動作？

指令碼動作是 Azure 叢集節點執行以進行設定變更或安裝軟體的艦隊指令碼。 指令碼的巨集指令執行為根，，並提供叢集節點的完整存取權限。

透過以下兩種方法可以套用指令碼動作︰

| 您可以使用此選項套用指令碼... | 期間叢集建立... | 在執行叢集... |
| ----- |:-----:|:-----:|
| Azure 入口網站 | ✓ | ✓ |
| Azure PowerShell | ✓ | ✓ |
| Azure CLI | &nbsp; | ✓ |
| HDInsight.NET SDK | ✓ | ✓ |
| Azure 資源管理員範本 | ✓ | &nbsp; |

如需有關如何使用這些方法來套用指令碼動作的詳細資訊，請參閱[自訂 HDInsight 叢集使用指令碼的動作](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="bestPracticeScripting"></a>指令碼開發的最佳作法

當您開發自訂指令碼 HDInsight 叢集時，有幾個謹記的最佳作法︰

- [目標 Hadoop 版本](#bPS1)
- [目標作業系統版本](#bps10)
- [提供穩定指令碼資源的連結](#bPS2)
- [使用預先編譯的資源](#bPS4)
- [請確定叢集自訂指令碼冪](#bPS3)
- [確保架構的可用性](#bPS5)
- [設定自訂的元件，可使用 Azure Blob 儲存體](#bPS6)
- [STDOUT 和 STDERR 撰寫資訊](#bPS7)
- [將檔案儲存為 ASCII，與 LF 行尾](#bps8)
- [若要復原暫時性錯誤使用重試邏輯](#bps9)

> [AZURE.IMPORTANT] 60 分鐘內，都必須完成的指令碼動作或他們會逾時。 期間節點佈建，指令碼會執行與其他安裝和設定程序。 資源，例如 CPU 時間或網路頻寬競爭可能會導致採取更長的時間完成不在您的開發環境中的指令碼。

### <a name="bPS1"></a>目標 Hadoop 版本

不同版本的 HDInsight 使用不同版本的 Hadoop 服務和安裝元件。 如果您的指令碼必須為特定版本的服務或元件，您應該只使用指令碼 HDInsight 包含所需的元件的版本。 您可以找到 HDInsight 隨附的元件版本上的資訊使用[HDInsight 元件版本設定](hdinsight-component-versioning.md)文件。

###<a name="bps10"></a>目標作業系統版本

Linux 型 HDInsight 根據 Ubuntu Linux 通訊群組。 使用不同版本的 Ubuntu，可能會影響您的指令碼的行為方式不同版本的 HDInsight。 例如，HDInsight 3.4 及舊版會根據使用同時 Ubuntu 版本。 3.5 版根據 Ubuntu 16.04，使用 Systemd。 Systemd 並同時，依賴不同的命令，因此應該使用同時撰寫指令碼。

另一個重要 HDInsight 3.4 和 3.5 之間的差別的`JAVA_HOME`現在指向 Java 8。

您可以使用檢查作業系統版本`lsb_release`。 下列程式碼片段色調安裝指令碼示範如何判斷上 Ubuntu 14 或 16 是否正在執行指令碼︰

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

您可以尋找完整包含指令碼，這些程式碼片段，在 https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh。

Ubuntu HDInsight 所使用的版本，請參閱[HDInsight 元件版本](hdinsight-component-versioning.md)文件。

若要瞭解 Systemd 和同時之間的差異，請參閱[Systemd 同時使用者](https://wiki.ubuntu.com/SystemdForUpstartUsers)。

### <a name="bPS2"></a>提供穩定指令碼資源的連結

請確定的指令碼和指令碼所使用的資源仍保留整個生命週期的叢集，都可以使用這些檔案的版本，不會變更期間。 如果叢集中加入新的節點縮放作業期間，這些資源所需。

最佳作法是要下載並保存在您的訂閱 Azure 儲存體帳戶中的所有項目。

> [AZURE.IMPORTANT] 使用的儲存空間帳戶必須是預設儲存的帳戶叢集或公用、 唯讀容器上任何其他儲存的帳戶。

例如，由 Microsoft 提供的範例會儲存在[https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/)儲存帳戶，也就是由 HDInsight 小組維護公用、 唯讀容器。

### <a name="bPS4"></a>使用預先編譯的資源

若要降低執行指令碼的時間，避免編譯程式碼資源的作業。 不過，預先編譯的資源，並在 Azure Blob 儲存體中儲存的二進位版本，讓它可以快速叢集從下載您的指令碼。

### <a name="bPS3"></a>請確定叢集自訂指令碼冪

必須是在執行多個時間時的指令碼意義的冪設計指令碼，它應該確保叢集會傳回相同的狀態，每次執行。

比方說，如果自訂指令碼上安裝應用程式，在 /usr/local/bin 其第一個執行，請然後在每一個後續執行指令碼應該檢查是否應用程式已經存在於 /usr/local/bin 位置之前與其他的後續步驟指令碼]。

### <a name="bPS5"></a>確保架構的可用性

Linux 型 HDInsight 叢集提供叢集，內的作用中的兩個標頭節點和指令碼動作會執行兩個節點。 如果您安裝的元件預期只有一個主節點，您必須設計的指令碼，只會在其中一個兩個標頭中的節點叢集安裝元件。

> [AZURE.IMPORTANT] 預設 HDInsight 的一部分安裝的服務的設計如有需要不過這項功能不延伸至自訂指令碼 ctions 透過安裝的元件，無法透過兩個標頭節點之間。 如果您需要安裝透過高可用性指令碼動作的元件，您必須實作自己使用兩種可用的標頭節點的容錯移轉機制。

### <a name="bPS6"></a>設定自訂的元件，可使用 Azure Blob 儲存體

您在叢集安裝的元件可能使用 Hadoop 分散式檔案系統 (HDFS) 儲存空間的預設設定。 HDInsight 會使用 Azure Blob 儲存體 (WASB) 作為預設儲存空間。 這會提供保存資料，即使叢集會刪除 HDFS 相容的檔案系統。 您應該設定，而不是 HDFS 使用 WASB 安裝元件。

例如，下列會複製 giraph examples.jar 檔案從本機檔案系統 WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>STDOUT 和 STDERR 撰寫資訊

STDOUT 和 STDERR 撰寫指令碼執行期間的資訊登入，並可使用 Ambari 網頁 UI 檢視。

> [AZURE.NOTE] Ambari 時，才會順利建立叢集，才能使用。 如果您使用指令碼動作叢集建立期間，建立失敗時，請參閱疑難排解[自訂 HDInsight 叢集使用指令碼動作](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)以其他方式存取登入的資訊。

大部分公用程式和安裝套件會將已資訊寫入 STDOUT 和 STDERR，但是您可能會想要新增額外記錄。 若要將文字傳送至 STDOUT 使用`echo`。 例如︰

        echo "Getting ready to install Foo"

根據預設，`echo`會傳送到 STDOUT 的字串。 若要將其導向至 STDERR，新增`>&2`之前`echo`。 例如︰

        >&2 echo "An error occurred installing Foo"

這會 STDERR (2) 重新導向到 STDOUT (1，因此未列於此處的預設會) 傳送的資訊。 如需有關 IO 重新導向的詳細資訊，請參閱[http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html)。

檢視指令碼動作所記錄的資訊的詳細資訊，請參閱 <<c0>自訂 HDInsight 叢集使用指令碼的巨集指令

###<a name="bps8"></a>將檔案儲存為 ASCII，與 LF 行尾

艦隊指令碼應該終止 LF 行儲存為 ASCII 格式。 如果檔案儲存為 utf-8，包括位元組順序標記開頭的檔案，或行尾的 CRLF，是常見的 Windows 編輯器，指令碼會失敗有類似下列錯誤︰

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>若要復原暫時性錯誤使用重試邏輯

下載安裝套件使用引起取得或透過網際網路傳送資料的其他動作的檔案時的動作也會暫時網路錯誤而可能會失敗。 例如，您通訊遠端資源可能還在容錯移轉至備份的節點。

若要讓您的指令碼同時暫時性錯誤，您可以實作重試邏輯。 以下是函數的會執行任何命令傳遞至該並 （如果命令失敗，） 再試一次最多三次的範例。 它會等待每次重試之間的兩個秒數。

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

以下是使用此函數的範例。

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>自訂指令碼協助方法

指令碼動作協助方法是公用程式撰寫自訂指令碼時，您可以使用。 這些都定義在[https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)，並可以包含在您使用下列的指令碼︰

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

如此可讓下列協助程式可供使用指令碼︰

| 協助程式的使用方式 | 描述 |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | 從來源 URL 下載檔案，以指定的檔案路徑。 根據預設，它不會覆寫現有的檔案。 |
| `untar_file TARFILE DESTDIR` | 會擷取 tar 檔案 (使用`-xf`，) 到目的目錄。 |
| `test_is_headnode` | 如果在叢集標頭節點，會傳回 1; 上執行否則，0。 |
| `test_is_datanode` | 如果目前的節點的資料 （工作） 節點，會傳回 1;否則，0。 |
| `test_is_first_datanode` | 如果目前的節點 （稱為 workernode0） 的第一個資料 （工作） 節點，會傳回 1;否則，0。 |
| `get_headnodes` | 傳回叢集 headnodes 的完整的網域名稱。 名稱會以逗號分隔。 空字串會傳回錯誤。 |
| `get_primary_headnode` | 取得主要 headnode 的完整的網域名稱。 空字串會傳回錯誤。 |
| `get_secondary_headnode` | 取得次要 headnode 的完整的網域名稱。 空字串會傳回錯誤。 |
| `get_primary_headnode_number` | 取得主要 headnode 的數字的尾碼。 空字串會傳回錯誤。 |
| `get_secondary_headnode_number` | 取得次要 headnode 的數字的尾碼。 空字串會傳回錯誤。 |

## <a name="commonusage"></a>一般使用模式

本節提供實作撰寫您自己的自訂指令碼時可能會遇到的常見使用模式的一些指引。

### <a name="passing-parameters-to-a-script"></a>參數傳遞給指令碼

在某些情況下，您的指令碼可能會要求參數。 例如，您可能需要叢集的管理員密碼，才能從 Ambari REST API 擷取資訊。

參數傳遞給指令碼稱為_位置參數_，並指派給`$1`第一個引數，`$2`秒，以及讓。 `$0`包含的指令碼本身的名稱。

做為參數傳遞給指令碼的值應該放單引號 （'），以便傳遞的值都會被視為常值，並特殊的處理方式不提供給包含的字元，例如 「 ！ 」。

### <a name="setting-environment-variables"></a>設定環境變數

設定環境變數是由下列步驟執行︰

    VARIABLENAME=value

VARIABLENAME 哪裡變數的名稱。 若要存取變數之後，請使用`$VARIABLENAME`。 例如，若要指定位置的參數為命名密碼的環境變數所提供的值，您會使用下列︰

    PASSWORD=$1

後續資訊的存取權無法再使用`$PASSWORD`。

設定的指令碼內的環境變數只存在於範圍內的指令碼。 在某些情況下，您可能需要新增的指令碼完畢後，會保留系統寬環境變數。 通常這是讓連接到透過 SSH 叢集使用者可以使用您的指令碼來安裝元件。 您可以藉由新增環境變數完成`/etc/environment`。 例如，下列新增__HADOOP\_CONF\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>儲存自訂指令碼的位置

用來自訂叢集的指令碼必須先將是預設儲存的帳戶叢集或，另一個儲存帳戶，容器中將公用唯讀狀態。 如果您的指令碼存取處的資源這些也必須公開存取 (至少公用唯讀狀態)。 舉個例說，您可能會想叢集使用下載檔案`download_file`。

儲存 Azure 儲存體帳戶可存取的檔案 （例如預設儲存帳戶），叢集，會提供快速存取，此儲存是在 Azure 網路。

### <a name="checking-the-operating-system-version"></a>檢查作業系統版本

不同版本的 HDInsight 依賴 Ubuntu 特定的版本。 可能就必須檢查的指令碼的作業系統版本之間的差異。 例如，您可能需要安裝連結至 Ubuntu 新版二進位。

若要檢查作業系統版本，請使用`lsb_release`。 例如，下列示範如何參考不同 tar 檔案，取決於作業系統版本︰

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>部署指令碼的巨集指令的檢查清單

以下是我們原本準備部署這些指令碼時的步驟︰

- 將檔案包含自訂指令碼部署期間叢集節點的可存取的位置。 這可能是任何預設或指定一次叢集部署或任何其他的公開存取存放容器的額外儲存空間帳戶。

- 新增到指令碼，以確保他們執行 impotently，以便指令碼可以多次執行相同的節點上檢查。

- 若要保留的指令碼所使用的下載的檔案，然後清除這些後執行指令碼使用暫存檔案目錄 /tmp。

- OS 層級設定] 或 [Hadoop 服務設定檔已變更，您可能會想要重新啟動 HDInsight 服務，讓他們可以選擇任何 OS 層級設定，例如設定中的指令碼的環境變數。

## <a name="runScriptAction"></a>如何執行指令碼的巨集指令

若要自訂 HDInsight 叢集使用 Azure 入口網站中，PowerShell 的 Azure，Azure 資源管理員 (ARM) 範本或 HDInsight.NET SDK，您可以使用指令碼動作。 如需相關指示，請參閱[如何使用指令碼的巨集指令](hdinsight-hadoop-customize-cluster-linux.md)。

## <a name="sampleScripts"></a>自訂指令碼範例

Microsoft 提供 HDInsight 叢集上安裝元件的指令碼範例。 範例指令碼及如何使用這些指示可在下面的連結︰

- [安裝並使用色調 HDInsight 叢集上](hdinsight-hadoop-hue-linux.md)
- [安裝並使用 R HDInsight Hadoop 叢集上](hdinsight-hadoop-r-scripts-linux.md)
- [安裝並使用 Solr HDInsight 叢集上](hdinsight-hadoop-solr-install-linux.md)
- [安裝並使用 Giraph HDInsight 叢集上](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] 上方連結的文件會有特定 Linux 型 HDInsight 叢集。 適用於 Windows 型 HDInsight 的指令碼，請參閱[使用 HDInsight (Windows) 的指令碼動作開發](hdinsight-hadoop-script-actions.md)或使用每篇文章的最上方的 [可用的連結。

##<a name="troubleshooting"></a>疑難排解

以下是使用您已經開發的指令碼時，可能會遇到的錯誤︰

__Error__: `$'\r': command not found`. 有時後面`syntax error: unexpected end of file`。

_原因_︰ 指令碼中的線條結尾 CRLF 時，會發生這個錯誤。 Unix 系統預期只 LF 為線條結尾。

發生此問題通常時指令碼是在 Windows 環境中，為 CRLF 線是一種常見的 Windows 上的多個文字編輯器結束。

_解析度_︰ 如果您在文字編輯器中的選項，請選取 Unix 格式或 LF 結尾行。 您也可能會變更 LF CRLF Unix 系統上使用下列命令︰

> [AZURE.NOTE] 下列命令是約略他們應該變更 LF CRLF 行尾。 選取其中一個根據系統上可用的公用程式。

| ] 命令 | 備忘稿 |
| ------- | ----- |
| `unix2dos -b INFILE` | 將備份原始檔案使用。BAK 副檔名 |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE 包含具有只 LF 尾的版本 |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | 這將會修改檔案直接但不建立新檔案 |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | OUTFILE 會包含具有只 LF 尾的版本。

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_原因_︰ 指令碼儲存為 utf-8 與位元組順序標記 (BOM) 時，會發生這個錯誤。

_解決方案_︰ 將檔案儲存為 ASCII，或不 BOM utf-8。 您也可以在 Linux 或 Unix 系統使用下列命令，以建立新的檔案不 BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

上方的命令，請將__INFILE__取代包含 BOM 的檔案。 __OUTFILE__應該是新的檔案名稱，其中會包含不 BOM 的指令碼。

## <a name="seeAlso"></a>後續步驟

* 瞭解如何[自訂 HDInsight 叢集使用指令碼的巨集指令](hdinsight-hadoop-customize-cluster-linux.md)

* 若要進一步瞭解建立管理 HDInsight 的.NET 應用程式使用[HDInsight.NET SDK 參考](https://msdn.microsoft.com/library/mt271028.aspx)

* 您可以使用 [ [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) ，瞭解如何使用其餘 HDInsight 叢集上執行管理動作。
