<properties
pageTitle="HDInsight 叢集建立期間新增登錄區文件庫 |Azure"
description="瞭解如何新增登錄區文件庫 （jar 檔案），為 HDInsight 叢集叢集建立期間。"
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>HDInsight 叢集建立期間新增登錄區文件庫

如果您有您經常使用登錄區 HDInsight 上的文件庫，這份文件包含預先載入叢集建立期間的 [文件庫中使用指令碼的巨集指令的資訊。 這也能讓文件庫全域可用在登錄區 （不需要使用載入他們的 [[新增 JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) 。）

##<a name="how-it-works"></a>運作方式

在建立叢集時，您可以選擇性地指定時所建立，叢集節點執行指令碼，指令碼動作。 這份文件中的指令碼接受一個參數，也就是包含文件庫 （儲存為 jar 檔案），將預先載入的 WASB 位置。

在叢集建立指令碼列舉的檔案，複製到`/usr/lib/customhivelibs/`目錄不對和工作者節點，再將它們新增至`hive.aux.jars.path`中的屬性`core-site.xml`檔案。 在 Linux 型叢集，它也會更新`hive-env.sh`檔案的檔案的位置。

> [AZURE.NOTE] 使用本文中的指令碼動作可讓您的文件庫可以在下列情況使用︰
>
> * __Linux 型 HDInsight__ -使用__登錄區的命令列__、 __WebHCat__ (Templeton) 及__HiveServer2__時。
> * __Windows 型 HDInsight__ -時使用的__登錄區的命令列__及__WebHCat__ (Templeton)。

##<a name="the-script"></a>指令碼

__指令碼位置__

__Linux 型__叢集︰ [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

若為__Windows 型叢集__︰ [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__需求__

* 指令碼必須套用至__不對節點__和__工作者節點__。

* 您想要安裝 （每瓶） 必須 Azure Blob 儲存體中儲存__單一容器__中。 

* 包含的__必須__jar 檔案的文件庫的儲存空間帳戶時建立連結至 HDInsight 叢集。 這可以完成兩種方法之一︰

    * 成為預設儲存帳戶叢集容器中。
    
    * 藉由容器中將連結的儲存空間容器上。 例如，在入口網站，您可以使用__選用的設定__，若要新增額外的儲存空間的__連結儲存帳戶__。

* 必須為參數，指令碼動作來指定容器 WASB 路徑。 例如，如果 （每瓶） 會儲存在名為__程式庫__儲存帳戶命名__mystorage__容器，就會參數__wasbs://libs@mystorage.blob.core.windows.net/__。

    > [AZURE.NOTE] 這份文件假設您已有建立儲存帳戶、 blob 容器，及上傳至的檔案。 
    >
    > 如果您還沒有建立儲存帳戶，您可以透過[Azure 入口網站](https://portal.azure.com)。 公用程式，例如[Azure 儲存檔案總管](http://storageexplorer.com/)然後可用來建立新的容器，在帳戶和上傳檔案到它。

##<a name="create-a-cluster-using-the-script"></a>建立使用指令碼

> [AZURE.NOTE] 下列步驟建立新的 Linux 型 HDInsight 叢集。 若要建立新的 windows 叢集， __Windows__和叢集 OS 建立時選取叢集，並使用 Windows (PowerShell) 指令碼，而不是艦隊指令碼。
> 
> 若要建立使用這個指令碼，您也可以使用 PowerShell 的 Azure 或 HDInsight.NET SDK。 如需有關如何使用這些方法的詳細資訊，請參閱[自訂 HDInsight 叢集指令碼動作](hdinsight-hadoop-customize-cluster-linux.md)。

1. 開始佈建叢集，依照步驟中[linux 佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)，但未完成佈建中。

2. 在**選用設定**刀中，選取 [**指令碼動作**]，然後提供的資訊，如下所示︰

    * __名稱__︰ 輸入指令碼動作易記的名稱。
    * __指令碼 URI__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __不對__︰ 核取此選項
    * __工作__︰ 核取此選項。
    * __動物園管理員__︰ 留白。
    * __參數__︰ 輸入包含 （每瓶） 的容器和儲存帳戶 WASB 地址。 例如， __wasbs://libs@mystorage.blob.core.windows.net/__。

3. 在**指令碼動作**底部，使用 [**選取**] 按鈕儲存設定。

4. 在**選擇性組態**刀中，選取__連結的儲存空間帳戶__，請選取 [__新增儲存空間金鑰__連結。 選取包含 （每瓶），儲存帳戶，然後使用 [__選取__] 按鈕儲存設定並返回__選用設定__刀。

5. 使用**選用設定**刀底部的 [**選取**] 按鈕，來儲存的選用的設定資訊。

6. 繼續佈建叢集， [linux 佈建 HDInsight 叢集](hdinsight-hadoop-provision-linux-clusters.md#portal)所述。

叢集建立完成後，您才可以使用這個指令碼透過新增從登錄區而不必使用 （每瓶）`ADD JAR`陳述式。

##<a name="next-steps"></a>後續步驟

在這份文件中您已經學會如何新增至 HDInsight 叢集的 jar 檔案中包含在叢集建立期間的登錄區文件庫。 更多使用群組的詳細資訊，請參閱[使用登錄區與 HDInsight](hdinsight-use-hive.md)
