<properties
pageTitle="DataFu 使用 HDInsight 的豬"
description="DataFu 是用於 Hadoop 的文件庫的集合。 瞭解如何使用 DataFu 與豬 HDInsight 叢集上。"
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
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="use-datafu-with-pig-on-hdinsight"></a>DataFu 使用 HDInsight 的豬

DataFu 是用於 Hadoop 開啟來源文件庫的集合。 在此文件中，您將學習如何使用 DataFu 上 HDInsight 叢集，以及如何使用豬 DataFu 使用者定義函數 (UDF)。

##<a name="prerequisites"></a>必要條件

* Azure 的訂閱。

* Azure HDInsight 叢集 （Linux 或 Windows 基礎）

* 熟悉[使用 HDInsight 的豬](hdinsight-use-pig.md)

##<a name="install-datafu-on-linux-based-hdinsight"></a>在 [Linux 型 HDInsight 上安裝 DataFu

> [AZURE.NOTE] DataFu 上所安裝 Linux 型叢集版本 3.3 及更新版本，以及在 Windows 型叢集上。 將其上未安裝 Linux 型叢集早於 3.3。
>
> 如果您使用 Linux 型叢集 3.3 或更新版本或 windows 叢集，您可以略過此節。

DataFu 可以下載並安裝從 Maven 存放庫。 若要新增 DataFu HDInsight 叢集使用下列步驟︰

1. 連接到使用 SSH Linux 型 HDInsight 叢集。 使用 HDInsight SSH 的詳細資訊，請參閱下列文件︰

    * [使用上從 Linux 與 OS X Unix HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. 若要下載 DataFu jar 檔案使用 wget 公用程式，請使用下列命令，或複製並將連結貼到您的瀏覽器，開始下載。

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. 接下來上, 傳檔案至您的 HDInsight 叢集的預設儲存空間。 如此可讓檔案供所有節點叢集，然後檔案會保留在儲存空間，即使您刪除並重新建立叢集]。

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] 上述範例會儲存在 jar`wasbs:///example/jars`由於此目錄已經存在叢集存放區。 您可以使用您想在 HDInsight 叢集存放區的任何位置。

##<a name="use-datafu-with-pig"></a>DataFu 使用的豬

本節中的步驟假設您熟悉使用豬 HDInsight，並只提供 [豬拉丁陳述式，而不使用方法與叢集上的步驟。 如需有關如何使用 HDInsight 豬的詳細資訊，請參閱[使用豬與 HDInsight](hdinsight-use-pig.md)。

> [AZURE.IMPORTANT] 使用時從豬 DataFu Linux 型 HDInsight 叢集上，您必須先註冊使用下列的豬拉丁陳述式 jar 檔案︰
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> 依預設位於 Windows 型 HDInsight 叢集登錄 DataFu。

您通常會定義 DataFu 函數的別名。 例如︰

    DEFINE SHA datafu.pig.hash.SHA();
    
定義具名別名`SHA`的 SHA 雜湊函數。 您可以再使用此豬拉丁指令碼來產生雜湊輸入資料。 例如，下列取代輸入的資料中的名稱雜湊值︰

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

如果這用於下列輸入的資料︰

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
它會產生下列輸出︰

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##<a name="next-steps"></a>後續步驟

如需有關 DataFu 或豬的詳細資訊，請參閱下列文件︰

* [Apache DataFu 豬指南](http://datafu.incubator.apache.org/docs/datafu/guide.html)。

* [使用 HDInsight 的豬](hdinsight-use-pig.md)
