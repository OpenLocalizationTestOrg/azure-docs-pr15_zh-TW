<properties
   pageTitle="Hadoop 上 HDInsight 與 MapReduce |Microsoft Azure"
   description="瞭解如何在 HDInsight 叢集，然後上 Hadoop 中執行 MapReduce 工作。 您會執行基本的 word 計算作業實作以 Java MapReduce 工作。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Hadoop 上 HDInsight 中使用 MapReduce

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

在本文中，您將學習如何在 Hadoop 上執行 MapReduce 工作，HDInsight 叢集。 我們執行基本的 word 計算作業實作以 Java MapReduce 工作。

##<a id="whatis"></a>什麼是 MapReduce？

Hadoop MapReduce 是軟體架構撰寫處理大量資料的工作。 輸入的資料分割成獨立的區塊，然後的平行處理各叢集節點。 包含兩個函數 MapReduce 工作︰

* **對應程式**︰ 使用輸入的資料、 分析 （通常是使用篩選及排序作業） 和發出 tuple （關鍵值組）
* **減壓器**︰ 使用 tuple 發出的對應程式，並執行建立更小的合併結果從的對應程式資料的摘要作業

基本 word 計算 MapReduce 工作範例] 下圖所示︰

![HDI。WordCountDiagram][image-hdi-wordcountdiagram]

這項工作的輸出是已分析的文字中的每個單字發生的次數的計數。

* 對應程式採用每行輸入做為輸入文字並將其字的文字。 它會發出索引鍵/值組的文字，word 就會發生每次後面為 1。 輸出會傳送到減壓器之前] 排序。

* 減壓器加總這些個別計算每個單字，因此會發出單一鍵/值組包含字，後面接著其發生次數的總和。

MapReduce 可以實作各種不同的語言。 Java 是最常見的實作，並用於此文件中的示範用。

### <a name="hadoop-streaming"></a>Hadoop 串流

語言或根據 Java 和 Java 虛擬機器 （例如，Scalding 或重疊顯示，） 都可以直接為 MapReduce 作業，類似於 Java 應用程式錯誤。 其他人，例如 C# 或 Python 或獨立可執行檔，必須使用 Hadoop 串流。

Hadoop 串流通訊的對應程式和減壓器 STDIN 和 STDOUT-的對應程式減壓器 STDIN，從一次讀取資料行和輸出寫入 STDOUT。 讀取或發出的對應程式及減壓器每一行必須是金鑰/值組，以] 索引標籤 charaacter 的格式︰

    [key]/t[value]

如需詳細資訊，請參閱[Hadoop 串流](http://hadoop.apache.org/docs/r1.2.1/streaming.html)。

如需使用 Hadoop 串流與 HDInsight 的範例，請參閱下列各項︰

* [開發 Python MapReduce 工作](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>範例資料的相關資訊

在此範例中，範例資料，您將使用所提供為文字中的文件 HDInsight 叢集的達文西，筆記本。

範例資料會儲存於 Azure Blob 儲存體，即為預設檔案系統使用 Hadoop 叢集的 HDInsight。 HDInsight 都可以存取使用**wasb**前置詞 Blob 儲存體中儲存的檔案。 例如，若要存取 sample.log 檔案，您會使用下列語法︰

    wasbs:///example/data/gutenberg/davinci.txt

因為 Azure Blob 儲存體 HDInsight 的預設儲存空間，您也可以使用**/example/data/gutenberg/davinci.txt**存取檔案。

> [AZURE.NOTE] 在先前的語法， **wasbs: / /**會用來存取儲存在 HDInsight 叢集的預設存放容器中的檔案。 如果您指定額外儲存空間帳戶，當您佈建叢集，而且您想要存取儲存在這些帳戶中的檔案時，您可以指定容器名稱和儲存帳戶地址來存取資料。 例如， **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**。

##<a id="job"></a>關於 MapReduce 的範例

在此範例中所 MapReduce 工作位於**wasbs://example/jars/hadoop-mapreduce-examples.jar**，並隨附 HDInsight 叢集。 此頁面包含您會針對**davinci.txt**執行 word 計算範例。

> [AZURE.NOTE] HDInsight 2.1 叢集上的檔案位置會是**wasbs:///example/jars/hadoop-examples.jar**。

參照的是 word 計算 MapReduce 工作 Java 程式碼︰

    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

      public static class TokenizerMapper
           extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
          StringTokenizer itr = new StringTokenizer(value.toString());
          while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
          }
        }
      }

      public static class IntSumReducer
           extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
                           ) throws IOException, InterruptedException {
          int sum = 0;
          for (IntWritable val : values) {
            sum += val.get();
          }
          result.set(sum);
          context.write(key, result);
        }
      }

      public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
          System.err.println("Usage: wordcount <in> <out>");
          System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
      }
    }

若要撰寫 MapReduce 工作的指示，請參閱[開發 Java MapReduce 程式的 HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)。

##<a id="run"></a>執行 MapReduce

HDInsight 可以使用各種不同的方法來執行 HiveQL 工作。 決定哪一種方法是最適合您，請使用下表，然後依照逐步解說中的連結]。

| **使用此**...                                                    | **...]。 若要執行此動作**                                       | ....with**叢集作業系統** | ....from**用戶端作業系統** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md)                       | 使用 [透過**SSH** Hadoop] 命令                  | Linux                                     | Linux、 Unix、 Mac OS X 或 Windows        |
| [捲曲](hdinsight-hadoop-use-mapreduce-curl.md)                     | 使用**其餘**遠端提交工作               | Linux 或 Windows                          | Linux、 Unix、 Mac OS X 或 Windows        |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | 使用**Windows PowerShell**遠端提交工作 | Linux 或 Windows                          | Windows                                  |
| [遠端桌面](hdinsight-hadoop-use-mapreduce-remote-desktop)    | 使用 [Hadoop] 命令，透過**遠端桌面**       | Windows                                   | Windows                                  |

##<a id="nextsteps"></a>後續步驟

雖然 MapReduce 提供功能強大的診斷能力，它可能有點挑戰母片。 有數種 Java 基礎架構，更容易定義 MapReduce 應用程式，以及技術，例如豬和登錄區，提供更容易的方式來使用 HDInsight 中的資料。 若要深入瞭解，請參閱下列文章︰

* [開發 HDInsight Java MapReduce 程式](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [開發 Python 的 HDInsight 串流 MapReduce 程式](hdinsight-hadoop-streaming-python.md)

* [開發上 HDInsight Apache Hadoop Scalding MapReduce 工作](hdinsight-hadoop-mapreduce-scalding.md)

* [使用 HDInsight 的登錄區][hdinsight-use-hive]

* [使用 HDInsight 的豬][hdinsight-use-pig]

* [執行 HDInsight 範例][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
