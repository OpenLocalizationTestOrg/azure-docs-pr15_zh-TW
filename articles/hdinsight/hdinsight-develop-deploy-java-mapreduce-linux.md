<properties
    pageTitle="開發 Linux 型 HDInsight Java MapReduce 程式 |Microsoft Azure"
    description="瞭解如何開發 Java MapReduce 程式，並將其部署到 Linux 型 HDInsight。"
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    authors="Blackmist"
    documentationCenter=""
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="larryfr"/>

# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Hadoop HDInsight linux 開發 Java MapReduce 程式

此文件會引導您使用 Apache Maven 建立 MapReduce 應用程式，然後部署及 HDInsight 叢集上 Linux 型 Hadoop 上執行。

##<a name="prerequisites"></a>必要條件

本教學課程之前，您必須具備下列項目︰

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 或更新版本 （或相同，例如 OpenJDK）

- [Apache Maven](http://maven.apache.org/)

- **Azure 的訂閱**

- **Azure CLI**

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

##<a name="configure-environment-variables"></a>設定環境變數

當您安裝 Java 和 JDK 時，可以設定下列環境變數。 不過，您應該檢查其存在於，並在其包含您的系統的正確值。

* **JAVA_HOME** -指向 Java 執行階段環境 (JRE) 安裝目錄。 例如，在 OS X、 Unix 或 Linux 系統中，應該類似值`/usr/lib/jvm/java-7-oracle`。 在 Windows 中，它會有類似值`c:\Program Files (x86)\Java\jre1.7`

* **路徑**-應該包含下列路徑︰

    * **JAVA_HOME**（或相當於路徑）

    * **JAVA_HOME\bin**（或相當於路徑）

    * 安裝 Maven 目錄

##<a name="create-a-new-maven-project"></a>建立新的 Maven 專案

1. 從終端機工作階段或在您的開發環境中的命令列中，將目錄變更為您要儲存此專案的位置。

3. 使用 [ __mvn__ ] 命令，這與 Maven 安裝，產生專案的臨時平台。

        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    這會在目前的目錄中，建立新的目錄名稱指定__artifactID__參數 (在此範例中的**wordcountjava** )。此目錄會包含下列項目︰

    * __pom.xml__ -[專案物件模型 (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)包含用來建立專案的資訊及設定詳細資料。

    * __src__ -包含__主要/java/組織/apache/hadoop/範例__目錄，您會在此撰寫應用程式的目錄。

3. 刪除__src/test/java/org/apache/hadoop/examples/apptest.java__檔案，因為它不會使用在此範例中。

##<a name="add-dependencies"></a>新增相依性

1. 編輯__pom.xml__檔案，並新增下列內`<dependencies>`區段︰

        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-client-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-common</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>

    這就是告訴 Maven 專案需要文件庫 (列內&lt;artifactId\>) 使用特定的版本 (列內&lt;版本\>)。 編譯時，這就會從下載預設 Maven 存放庫。 若要檢視更多，您可以使用[Maven 存放庫搜尋](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar)。

    `<scope>provided</scope>`會告訴 Maven 不應應用程式，封裝這些相依性所 HDInsight 叢集在執行階段會提供。

2. 將下列文字新增至__pom.xml__檔案。 這必須是內部`<project>...</project>`檔案; 的標籤例如，之間`</dependencies>`和`</project>`。

        <build>
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.3</version>
              <configuration>
                <transformers>
                  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
              </configuration>
              <executions>
                <execution>
                  <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                </execution>
              </executions>
            </plugin>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
          </plugins>
        </build>

    第一個外掛程式設定[Maven 上網底外掛程式](http://maven.apache.org/plugins/maven-shade-plugin/)，這用來建立 uberjar （有時稱為 fatjar），其中包含應用程式所需的相依性。 也可以防止重複將某些系統可能會導致問題的 jar 封裝中的授權。

    第二個外掛程式設定 Maven 編譯器，用來設定 Java HDInsight 叢集上使用的版本此應用程式所需的版本。

3. 儲存__pom.xml__檔案。

##<a name="create-the-mapreduce-application"></a>建立 MapReduce 應用程式

1. 移至 [ __wordcountjava/src/主要/java/組織/apache/hadoop/範例__目錄，然後重新命名__WordCount.java__ __App.java__檔案。

2. 文字編輯器中開啟__WordCount.java__檔案，並以下列取代內容︰

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

    請注意套件名稱**org.apache.hadoop.examples** ，而的類別名稱會**WordCount**。 當您送出 MapReduce 工作，您會使用下列名稱。

3. 儲存檔案。

##<a name="build-the-application"></a>建置應用程式

1. 如果您不是已有，變更__wordcountjava__目錄。

2. 若要建立 JAR 檔案包含應用程式中使用下列命令︰

        mvn clean package

    這會清除上一個的任何建立成品，請下載的相依性已經尚未安裝，然後建立並封裝應用程式。

3. 一旦完成] 命令， __wordcountjava/目標__目錄會包含一個名為__wordcountjava-1.0-SNAPSHOT.jar__檔案。

    > [AZURE.NOTE] __wordcountjava-1.0-SNAPSHOT.jar__檔案是 uberjar，其中包含而不是只是 WordCount 工作的工作需要在執行階段的相依性。


##<a id="upload"></a>上傳糖

您可以使用下列命令，jar 檔案上傳至 HDInsight headnode:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

這會從本機系統複製檔案到主節點。

> [AZURE.NOTE] 如果您是使用密碼保護 SSH 帳號，系統會提示您輸入密碼。 如果您使用 SSH 鍵，您可能必須使用`-i`參數和私密金鑰的路徑。 例如， `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`。

##<a name="run"></a>執行 MapReduce 工作

1. 連線至 HDInsight 使用 SSH 下列文章中所述︰

    - [使用上 HDInsight Linux、 Unix，或 OS X 的 Linux 型 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [使用上從 Windows HDInsight Linux 為基礎的 Hadoop SSH](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 從 SSH 工作階段，請執行 MapReduce 應用程式中使用下列命令︰

        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout

    這會計算字數 davinci.txt 檔案，請使用 WordCount MapReduce 應用程式，並儲存結果__wasbs: / 範例/資料/wordcountout__。 輸入的檔案和輸出會儲存在叢集預設儲存空間。

3. 工作完成後，請使用下列檢視結果︰

        hdfs dfs -cat wasbs:///example/data/wordcountout/*

    您應該會收到文字與項目個數，類似以下的值的清單︰

        zeal    1
        zelus   1
        zenith  2

##<a id="nextsteps"></a>後續步驟

在此文件中，您已經學會如何開發 Java MapReduce 工作。 請參閱下列文件以其他方式來使用 HDInsight。

- [使用 HDInsight 的登錄區][hdinsight-use-hive]
- [使用 HDInsight 的豬][hdinsight-use-pig]
- [使用 HDInsight MapReduce](hdinsight-use-mapreduce.md)

如需詳細資訊，請參閱[Java 開發人員中心](https://azure.microsoft.com/develop/java/)。

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

